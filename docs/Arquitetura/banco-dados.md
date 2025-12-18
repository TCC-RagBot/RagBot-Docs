# Banco de Dados

## Visão Geral

O sistema utiliza PostgreSQL 15 com a extensão pgvector para armazenar tanto dados relacionais quanto embeddings vetoriais. Essa abordagem híbrida elimina a necessidade de múltiplos bancos de dados e simplifica a arquitetura.

## Estratégia de Armazenamento

### Dados Relacionais
- Conversas e mensagens do chat
- Metadados dos documentos processados
- Chunks de origem das respostas

### Dados Vetoriais
- Embeddings dos chunks de documentos (384 dimensões)
- Índices para busca por similaridade
- Gerenciados automaticamente pelo LangChain

## Esquema do Banco de Dados

```mermaid
erDiagram
    conversations ||--o{ messages : contains
    messages ||--o{ message_source_chunks : references
    documents {
        uuid id PK
        text filename
        int chunks_count
        bigint file_size_bytes
        timestamptz processed_at
        timestamptz created_at
    }
    conversations {
        uuid id PK
        timestamptz created_at
    }
    messages {
        uuid id PK
        uuid conversation_id FK
        text role
        text content
        timestamptz created_at
    }
    message_source_chunks {
        uuid message_id FK
        text chunk_content
        text document_name
        float similarity_score
    }
```

## Tabelas Detalhadas

### 1. conversations

Armazena as conversas dos usuários.

```sql
CREATE TABLE conversations (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Campos:**
- `id`: Identificador único da conversa
- `created_at`: Data/hora de criação

**Propósito:**
- Agrupar mensagens de uma mesma sessão de chat
- Manter contexto de conversações
- Permitir histórico de interações

### 2. messages

Armazena todas as mensagens trocadas (usuário e assistente).

```sql
CREATE TABLE messages (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    conversation_id UUID NOT NULL REFERENCES conversations(id) ON DELETE CASCADE,
    role TEXT NOT NULL CHECK (role IN ('user', 'assistant')),
    content TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Campos:**
- `id`: Identificador único da mensagem
- `conversation_id`: Referência à conversa
- `role`: Quem enviou (`user` ou `assistant`)
- `content`: Conteúdo da mensagem
- `created_at`: Timestamp da mensagem

**Propósito:**
- Registrar perguntas do usuário
- Armazenar respostas geradas
- Permitir histórico completo de conversas

**Relacionamentos:**
- `ON DELETE CASCADE`: Se conversa for deletada, mensagens são removidas

### 3. message_source_chunks

Relaciona mensagens com os chunks de documentos usados como fonte.

```sql
CREATE TABLE message_source_chunks (
    message_id UUID NOT NULL REFERENCES messages(id) ON DELETE CASCADE,
    chunk_content TEXT NOT NULL,
    document_name TEXT NOT NULL,
    similarity_score FLOAT,
    PRIMARY KEY (message_id, chunk_content)
);
```

**Campos:**
- `message_id`: Referência à mensagem do assistente
- `chunk_content`: Trecho do documento utilizado
- `document_name`: Nome do arquivo PDF de origem
- `similarity_score`: Score de similaridade (0-1)

**Propósito:**
- Rastreabilidade das respostas
- Citação de fontes
- Auditoria de qualidade das respostas

### 4. documents

Metadados dos documentos processados.

```sql
CREATE TABLE documents (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    filename TEXT NOT NULL UNIQUE,
    chunks_count INTEGER NOT NULL,
    file_size_bytes BIGINT NOT NULL,
    processed_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Campos:**
- `id`: Identificador único do documento
- `filename`: Nome do arquivo (único)
- `chunks_count`: Quantidade de chunks gerados
- `file_size_bytes`: Tamanho do arquivo em bytes
- `processed_at`: Quando foi processado
- `created_at`: Data de upload

**Propósito:**
- Listar documentos disponíveis
- Evitar reprocessamento duplicado
- Estatísticas de processamento

**Restrições:**
- `filename` é UNIQUE (não permite duplicatas)

## pgvector: Busca Vetorial

### O que é pgvector?

pgvector é uma extensão do PostgreSQL que adiciona suporte nativo para:

- Armazenamento de vetores de alta dimensão
- Operações de similaridade (cosseno, L2, produto interno)
- Índices otimizados para busca vetorial

### Por que usar pgvector?

**Integração nativa:**

- Tudo em um único banco de dados
- Transações ACID para dados vetoriais
- Sem sincronização entre bancos diferentes

**Performance:**

- Algoritmo HNSW (Hierarchical Navigable Small World)
- Busca aproximada eficiente em milhões de vetores
- Índices otimizados para high-dimensional data

**Simplicidade operacional:**

- Backup e restore unificados
- Ferramentas existentes do PostgreSQL funcionam
- Deployment simplificado

### Tabela de Embeddings

O LangChain gerencia automaticamente a tabela `langchain_pg_embedding`:

```sql
CREATE TABLE langchain_pg_embedding (
    id UUID PRIMARY KEY,
    collection_id UUID,
    embedding VECTOR(384),  -- Dimensão do all-MiniLM-L6-v2
    document TEXT,
    cmetadata JSONB
);
```

**Campos importantes:**

- `embedding`: Vetor de 384 dimensões
- `document`: Conteúdo textual do chunk
- `cmetadata`: Metadados JSON (filename, page, etc)

### Estratégia de Busca

O sistema usa **similaridade de cosseno** para busca:

```python
results = vector_store.similarity_search_with_score(
    query="pergunta do usuário",
    k=5  # Top 5 chunks mais relevantes
)
```

**Funcionamento:**

1. Pergunta do usuário é convertida em embedding (384 dims)
2. pgvector calcula distância cosseno entre query e todos chunks
3. Retorna top-k chunks mais similares com scores
4. Chunks são usados como contexto para o LLM

### Índices para Performance

pgvector cria índices automaticamente:

```sql
CREATE INDEX ON langchain_pg_embedding 
USING hnsw (embedding vector_cosine_ops);
```

**Algoritmo HNSW:**

- Navegação hierárquica em grafo
- Busca aproximada em tempo logarítmico
- Trade-off entre precisão e velocidade

## Fluxo de Dados

### Ingestão de Documentos

```mermaid
sequenceDiagram
    participant Script as ingest.py
    participant PG as PostgreSQL
    participant Vec as pgvector
    
    Script->>PG: INSERT INTO documents (metadados)
    Script->>Vec: INSERT embeddings (LangChain)
    Vec->>Vec: Atualiza índices HNSW
    Vec-->>Script: Confirmação
```

### Busca e Resposta

```mermaid
sequenceDiagram
    participant API as FastAPI
    participant Vec as pgvector
    participant PG as PostgreSQL
    participant Gemini as Gemini AI
    
    API->>Vec: similarity_search(query)
    Vec->>Vec: Busca por similaridade
    Vec-->>API: Top-k chunks
    API->>Gemini: generate(prompt + chunks)
    Gemini-->>API: Resposta
    API->>PG: INSERT message + sources
```

## Configuração e Deployment

### Docker Compose

O banco é provisionado via Docker:

```yaml
services:
  db:
    image: pgvector/pgvector:pg16
    environment:
      POSTGRES_DB: ragbot_db
      POSTGRES_USER: tccrag
      POSTGRES_PASSWORD: tcc123
    ports:
      - "5433:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql
```

**Vantagens:**

- Imagem oficial com pgvector pré-instalado
- Schema criado automaticamente via init.sql
- Persistência via volume Docker
- Fácil reset do ambiente

### Inicialização Automática

O script `init.sql` é executado na primeira inicialização:

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "vector";

-- Criar tabelas...
```

### Conexão da Aplicação

FastAPI conecta via string de conexão:

```python
DATABASE_URL = "postgresql://tccrag:tcc123@localhost:5433/ragbot_db"

# Validação na inicialização
if not db_manager.test_connection():
    raise RuntimeError("Conexão com banco falhou")
```