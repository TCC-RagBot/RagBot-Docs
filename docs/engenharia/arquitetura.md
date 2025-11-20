# Arquitetura do Sistema RAGBot

## 📐 Visão Arquitetural Geral

O RAGBot foi projetado seguindo os princípios de **arquitetura limpa** e **separação de responsabilidades**, implementando uma solução robusta e escalável para processamento de documentos e geração de respostas inteligentes.

### 🎯 Princípios Arquiteturais

- **🔄 Separação de Responsabilidades**: Cada camada tem uma responsabilidade específica
- **🔌 Baixo Acoplamento**: Módulos independentes e facilmente testáveis
- **📈 Escalabilidade**: Arquitetura preparada para crescimento horizontal
- **🛡️ Segurança**: Validação rigorosa em todas as camadas
- **⚡ Performance**: Otimizações para busca vetorial e processamento de documentos

---

## 🏗️ Arquitetura de Alto Nível

```mermaid
graph TB
    subgraph "Cliente/Browser"
        UI[Interface Vue.js]
        Store[Pinia Store]
    end
    
    subgraph "API Gateway/Load Balancer"
        LB[NGINX/Traefik]
    end
    
    subgraph "Backend Services"
        API[FastAPI Application]
        Chat[Chat Service]
        Doc[Document Service]
        Vector[Vector Service]
    end
    
    subgraph "Data Layer"
        DB[(PostgreSQL + pgvector)]
        Files[Armazenamento de Arquivos]
    end
    
    subgraph "External AI Services"
        Gemini[Google Gemini AI]
        Embeddings[sentence-transformers]
    end
    
    UI --> Store
    Store --> LB
    LB --> API
    API --> Chat
    API --> Doc
    Chat --> Vector
    Doc --> Vector
    Vector --> DB
    Vector --> Files
    Chat --> Gemini
    Vector --> Embeddings
    
    classDef frontend fill:#4FC08D,stroke:#333,stroke-width:2px,color:#fff
    classDef backend fill:#FF6B6B,stroke:#333,stroke-width:2px,color:#fff
    classDef data fill:#4ECDC4,stroke:#333,stroke-width:2px,color:#fff
    classDef ai fill:#FFE66D,stroke:#333,stroke-width:2px,color:#000
    
    class UI,Store frontend
    class API,Chat,Doc,Vector backend
    class DB,Files data
    class Gemini,Embeddings ai
```

---

## 🎨 Frontend - Vue.js 3 Architecture

### 📱 Arquitetura de Componentes

```mermaid
graph TD
    subgraph "Aplicação Vue.js"
        App[App.vue]
        Router[Vue Router]
        
        subgraph "Módulo Chat"
            ChatView[ChatView.vue]
            ChatBubble[ChatBubble.vue]
            MessageInput[MessageInput.vue]
            TypingIndicator[TypingIndicator.vue]
            ChatStore[chatStore.ts]
            ChatAPI[chat/api.ts]
        end
        
        subgraph "Módulo Documentos"
            DocView[DocumentsView.vue]
            DocList[DocumentList.vue]
            DocAPI[documents/api.ts]
        end
        
        subgraph "Componentes Shared"
            Header[HeaderBar.vue]
            Theme[Theme Service]
        end
    end
    
    App --> Router
    Router --> ChatView
    Router --> DocView
    ChatView --> ChatBubble
    ChatView --> MessageInput
    ChatView --> TypingIndicator
    ChatView --> Header
    ChatStore --> ChatAPI
    DocView --> DocList
    DocView --> Header
    Header --> Theme
    
    classDef view fill:#4FC08D,stroke:#333,stroke-width:2px,color:#fff
    classDef component fill:#68D391,stroke:#333,stroke-width:2px,color:#000
    classDef service fill:#81C784,stroke:#333,stroke-width:2px,color:#000
    
    class ChatView,DocView view
    class ChatBubble,MessageInput,TypingIndicator,DocList,Header component
    class ChatStore,ChatAPI,DocAPI,Theme service
```

### 🔧 Tecnologias Frontend

| Componente | Tecnologia | Versão | Responsabilidade |
|------------|------------|--------|------------------|
| **Framework** | Vue.js | 3.5+ | Reatividade e componentes |
| **Build Tool** | Vite | 7.1+ | Bundling e dev server |
| **Roteamento** | Vue Router | 4.5+ | SPA routing |
| **Estado** | Pinia | 3.0+ | State management |
| **Styling** | Tailwind CSS | 3.4+ | Utility-first CSS |
| **Type Safety** | TypeScript | 5.9+ | Tipagem estática |
| **Markdown** | Marked | 16.3+ | Renderização de respostas |

---

## ⚙️ Backend - FastAPI Architecture

### 🏛️ Clean Architecture Implementation

```mermaid
graph TD
    subgraph "Presentation Layer"
        Routes[Route Handlers]
        Schemas[Pydantic Schemas]
        Middleware[Middlewares]
    end
    
    subgraph "Application Layer"
        ChatService[Chat Service]
        DocService[Document Service]
        RAGProcessor[RAG Processor]
    end
    
    subgraph "Domain Layer"
        Models[Domain Models]
        Interfaces[Repository Interfaces]
        BusinessLogic[Business Rules]
    end
    
    subgraph "Infrastructure Layer"
        Repositories[Concrete Repositories]
        Database[Database Manager]
        External[External APIs]
    end
    
    Routes --> Schemas
    Routes --> ChatService
    Routes --> DocService
    ChatService --> RAGProcessor
    DocService --> RAGProcessor
    RAGProcessor --> Models
    RAGProcessor --> Interfaces
    Interfaces --> Repositories
    Repositories --> Database
    RAGProcessor --> External
    
    classDef presentation fill:#FF6B6B,stroke:#333,stroke-width:2px,color:#fff
    classDef application fill:#4ECDC4,stroke:#333,stroke-width:2px,color:#000
    classDef domain fill:#45B7D1,stroke:#333,stroke-width:2px,color:#fff
    classDef infrastructure fill:#96CEB4,stroke:#333,stroke-width:2px,color:#000
    
    class Routes,Schemas,Middleware presentation
    class ChatService,DocService,RAGProcessor application
    class Models,Interfaces,BusinessLogic domain
    class Repositories,Database,External infrastructure
```

### 📁 Estrutura de Diretórios Backend

```
app/
├── __init__.py                 # Inicialização do módulo
├── main.py                     # Entry point da aplicação
├── application.py              # Configuração FastAPI
│
├── config/                     # Configurações
│   ├── settings.py            # Variáveis de ambiente
│   └── constants.py           # Constantes da aplicação
│
├── routes/                     # Camada de Apresentação
│   ├── core_routes.py         # Health check e métricas
│   ├── chat_routes.py         # Endpoints de chat
│   └── document_routes.py     # Endpoints de documentos
│
├── schemas/                    # Validação de Dados
│   ├── chat_schemas.py        # DTOs de chat
│   ├── document_schemas.py    # DTOs de documentos
│   └── shared_schemas.py      # DTOs compartilhados
│
├── services/                   # Camada de Aplicação
│   ├── chat_service.py        # Lógica de negócio do chat
│   └── document_service.py    # Lógica de negócio de documentos
│
└── repositories/              # Camada de Dados
    ├── chat_repository.py     # Persistência de conversas
    ├── document_repository.py # Persistência de documentos
    └── vector_repository.py   # Operações vetoriais
```

---

## 🗄️ Arquitetura de Dados

### 📊 Modelo Conceitual

```mermaid
erDiagram
    Document ||--o{ Chunk : "é dividido em"
    Chunk ||--|| Embedding : "possui"
    Conversation ||--o{ Message : "contém"
    Message ||--o{ MessageSource : "referencia"
    MessageSource }o--|| Chunk : "aponta para"
    
    Document {
        uuid id PK
        string filename
        string content_type
        int file_size
        text original_content
        timestamp created_at
        timestamp updated_at
    }
    
    Chunk {
        uuid id PK
        uuid document_id FK
        text content
        int chunk_index
        int start_char
        int end_char
        timestamp created_at
    }
    
    Embedding {
        uuid id PK
        uuid chunk_id FK
        vector embedding_vector
        string model_name
        timestamp created_at
    }
    
    Conversation {
        uuid id PK
        string title
        timestamp created_at
        timestamp updated_at
    }
    
    Message {
        uuid id PK
        uuid conversation_id FK
        text content
        string role
        float processing_time
        timestamp created_at
    }
    
    MessageSource {
        uuid id PK
        uuid message_id FK
        uuid chunk_id FK
        float similarity_score
        int rank
    }
```

### 🔍 Índices e Otimizações

```sql
-- Índices para Performance
CREATE INDEX idx_chunks_document_id ON chunks(document_id);
CREATE INDEX idx_embeddings_chunk_id ON embeddings(chunk_id);
CREATE INDEX idx_messages_conversation_id ON messages(conversation_id);
CREATE INDEX idx_messages_created_at ON messages(created_at DESC);

-- Índice HNSW para busca vetorial (pgvector)
CREATE INDEX ON embeddings USING hnsw (embedding_vector vector_cosine_ops);

-- Índice GIN para busca textual
CREATE INDEX idx_chunks_content_gin ON chunks USING gin(to_tsvector('portuguese', content));
```

---

## 🤖 Fluxo RAG (Retrieval-Augmented Generation)

### 📄 Processo de Ingestão de Documentos

```mermaid
sequenceDiagram
    participant U as Usuário
    participant F as Frontend
    participant API as FastAPI
    participant DS as Document Service
    participant VS as Vector Service
    participant DB as PostgreSQL
    participant AI as sentence-transformers
    
    U->>F: Upload PDF
    F->>API: POST /api/documents/upload
    API->>DS: process_document()
    DS->>DS: validate_pdf()
    DS->>DS: extract_text()
    DS->>VS: create_chunks()
    VS->>VS: split_text()
    loop Para cada chunk
        VS->>AI: generate_embedding()
        AI-->>VS: embedding_vector
        VS->>DB: save_chunk_and_embedding()
    end
    DB-->>VS: success
    VS-->>DS: chunks_created
    DS-->>API: document_processed
    API-->>F: 200 OK + document_id
    F-->>U: "Documento processado!"
```

### 💬 Processo de Chat/Pergunta

```mermaid
sequenceDiagram
    participant U as Usuário
    participant F as Frontend
    participant API as FastAPI
    participant CS as Chat Service
    participant VS as Vector Service
    participant DB as PostgreSQL
    participant AI as Google Gemini
    
    U->>F: Digita pergunta
    F->>API: POST /api/chat
    API->>CS: process_chat()
    CS->>VS: semantic_search()
    VS->>VS: generate_query_embedding()
    VS->>DB: vector_similarity_search()
    DB-->>VS: relevant_chunks[]
    VS-->>CS: context_chunks[]
    CS->>CS: build_prompt()
    CS->>AI: generate_response()
    AI-->>CS: ai_response
    CS->>DB: save_conversation()
    CS-->>API: chat_response
    API-->>F: 200 OK + response
    F-->>U: Exibe resposta
```

---

## 🔧 Componentes e Responsabilidades

### 🎯 Chat Service
**Responsabilidades:**
- Orquestrar o fluxo completo de chat
- Gerenciar contexto de conversações
- Integração com modelo de IA
- Validação de entrada e saída

**Métodos Principais:**
```python
async def process_chat(
    user_message: str,
    max_chunks: int = 5,
    conversation_id: Optional[UUID] = None
) -> ChatResponse
```

### 📄 Document Service
**Responsabilidades:**
- Upload e validação de documentos
- Processamento e extração de texto
- Gerenciamento de metadados
- Limpeza e organização de arquivos

**Métodos Principais:**
```python
def list_documents() -> DocumentListResponse
def delete_document(document_id: UUID) -> DocumentDeleteResponse
async def upload_document(file: UploadFile) -> DocumentUploadResponse
```

### 🔍 Vector Repository
**Responsabilidades:**
- Geração de embeddings
- Busca por similaridade semântica
- Otimização de queries vetoriais
- Cache de resultados

**Métodos Principais:**
```python
async def semantic_search(
    query: str,
    limit: int = 5,
    similarity_threshold: float = 0.7
) -> List[ChunkResult]
```

---

## 🛡️ Padrões de Segurança

### 🔐 Validação e Sanitização

| Camada | Mecanismo | Implementação |
|--------|-----------|---------------|
| **Frontend** | Input Validation | Vue.js reactive validation |
| **API** | Pydantic Schemas | Type validation + sanitization |
| **Serviços** | Business Rules | Domain-specific validation |
| **Banco** | Constraints | FK constraints + data integrity |

### 🚫 Proteções Implementadas

- ✅ **CORS configurado** para origens específicas
- ✅ **Rate limiting** em endpoints críticos  
- ✅ **Validação de tipos** com Pydantic
- ✅ **Sanitização de uploads** (apenas PDFs)
- ✅ **Logging detalhado** para auditoria
- ✅ **Environment variables** para secrets

---

## 📈 Escalabilidade e Performance

### 🚀 Otimizações Implementadas

1. **Database Performance**
   - Índices otimizados para busca vetorial (HNSW)
   - Connection pooling com SQLAlchemy
   - Query optimization para busca semântica

2. **API Performance**  
   - Async/await em todas as operações I/O
   - Pydantic para serialização rápida
   - Logging estruturado com loguru

3. **Frontend Performance**
   - Code splitting com Vite
   - Lazy loading de componentes
   - State management otimizado com Pinia

### 📊 Métricas de Performance

| Operação | Tempo Esperado | Otimização |
|----------|----------------|------------|
| **Upload PDF** | < 5s | Processamento assíncrono |
| **Busca Semântica** | < 500ms | Índice HNSW + cache |
| **Geração de Resposta** | < 3s | API streaming (futuro) |
| **Load Página** | < 2s | Code splitting + CDN |

---

## 🔮 Evolução da Arquitetura

### 📋 Próximas Versões

1. **v1.1 - Streaming**
   - Server-Sent Events para respostas em tempo real
   - Progress indicators durante processamento

2. **v1.2 - Microserviços**
   - Separação em serviços independentes
   - Message queue (Redis/RabbitMQ)

3. **v1.3 - Observabilidade**
   - Metrics com Prometheus
   - Distributed tracing
   - Health checks avançados

4. **v2.0 - Multi-tenancy**
   - Suporte a múltiplas organizações
   - Isolamento de dados por tenant
   - Admin dashboard

---

!!! success "Arquitetura Robusta e Escalável"
    A arquitetura do RAGBot foi projetada para ser **maintível**, **testável** e **escalável**, seguindo as melhores práticas de engenharia de software e preparada para evolução contínua.

Descrição da arquitetura proposta para o RagBot, incluindo componentes, integrações e decisões técnicas relevantes.
