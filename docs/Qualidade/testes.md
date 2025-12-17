# Testes e Garantia de Qualidade

## Estratégia de Testes

O projeto adotou uma abordagem mista de testes, combinando testes automatizados e manuais para garantir a qualidade do sistema.

### Foco em Testes de Integração

A estratégia priorizou **testes de integração** para validar funcionalidades completas do sistema, especialmente o pipeline RAG, que é o core do projeto.

**Justificativa:**

- Verificar fluxos end-to-end funcionando
- Garantir integração entre componentes (PyPDF, embeddings, pgvector, Gemini)
- Validar o comportamento real do sistema

## Framework de Testes - pytest

O backend utiliza **pytest** como framework principal para testes automatizados.

**Vantagens do pytest:**

- Sintaxe simples e pythônica
- Fixtures para setup/teardown
- Marcadores para categorizar testes
- Relatórios detalhados
- Fácil integração com CI/CD

### Estrutura de Testes

```
tests/
├── conftest.py           # Configurações e fixtures compartilhadas
├── test_ingestion.py     # Testes do fluxo de ingestão
└── __init__.py
```

## Casos de Teste Implementados

### 1. Fluxo de Ingestão de Documentos

**Objetivo:** Validar o processo completo de upload e indexação de PDFs.

**Cenários testados:**

**Teste 1: Carregamento de PDF**

- Verifica se o PyPDFLoader consegue ler o documento
- Valida que todas as páginas foram extraídas
- Confirma que o conteúdo não está vazio

```python
def test_pdf_loading_with_pypdf(test_pdf_path):
    loader = PyPDFLoader(test_pdf_path)
    documents = loader.load()
    
    assert documents is not None
    assert len(documents) > 0
    assert len(documents[0].page_content.strip()) > 0
```

**Teste 2: Chunking de Documentos**

- Verifica divisão do texto em chunks
- Valida tamanho dos chunks (≤1200 caracteres)
- Confirma que metadados são preservados

```python
def test_document_chunking(test_pdf_path):
    loader = PyPDFLoader(test_pdf_path)
    documents = loader.load()
    
    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=1000, 
        chunk_overlap=150
    )
    chunks = text_splitter.split_documents(documents)
    
    assert len(chunks) > 0
    assert all(len(chunk.page_content) <= 1200 for chunk in chunks)
```

**Teste 3: Geração de Embeddings**

- Verifica criação de vetores 384-dimensionais
- Valida que modelo all-MiniLM-L6-v2 funciona
- Confirma formato dos embeddings

```python
def test_embeddings_generation():
    embeddings_model = SentenceTransformerEmbeddings()
    test_texts = ["Texto de exemplo para testar embeddings."]
    
    embeddings = embeddings_model.embed_documents(test_texts)
    
    assert len(embeddings) == len(test_texts)
    assert len(embeddings[0]) == 384  # Dimensão do modelo
    assert all(isinstance(x, float) for x in embeddings[0])
```

### 2. Fluxo de Busca e Resposta

**Objetivo:** Validar o processo de query e geração de respostas.

**Cenários testados:**

**Busca Vetorial**

- Verifica similaridade semântica funciona
- Valida retorno dos top-k chunks
- Confirma scores de similaridade

**Geração de Respostas**

- Verifica integração com Gemini AI
- Valida formato da resposta
- Confirma citação de fontes

### 3. Persistência de Dados

**Objetivo:** Validar operações no banco de dados.

**Cenários testados:**

- Criação de conversas
- Salvamento de mensagens
- Armazenamento de source chunks
- Metadados de documentos

## Testes Manuais

Além dos testes automatizados, foram realizados **testes manuais** sistemáticos:

### Testes Funcionais

**Upload de Documentos:**

- PDFs de diferentes tamanhos
- Documentos com acentuação
- Arquivos com imagens

**Chat e Perguntas:**

- Perguntas simples e complexas
- Queries fora do escopo dos documentos
- Histórico de conversas

**Interface:**

- Navegação entre páginas
- Responsividade em mobile
- Modo claro/escuro

### Testes de Usabilidade

**Experiência do Usuário:**

- Fluidez da interação
- Clareza das respostas
- Visualização de fontes
- Feedbacks visuais

## Tabela de Casos de Teste

| ID | Categoria | Descrição | Tipo | Status |
|----|-----------|-----------|------|--------|
| **T01** | Ingestão | Carregar PDF com PyPDFLoader | Automatizado | ✅ Passou |
| **T02** | Ingestão | Dividir documento em chunks | Automatizado | ✅ Passou |
| **T03** | Ingestão | Gerar embeddings 384-dim | Automatizado | ✅ Passou |
| **T04** | Ingestão | Salvar embeddings no pgvector | Automatizado | ✅ Passou |
| **T05** | Busca | Busca semântica retorna chunks relevantes | Automatizado | ✅ Passou |
| **T06** | Busca | Scores de similaridade corretos | Automatizado | ✅ Passou |
| **T07** | API | Endpoint /ask retorna resposta válida | Manual | ✅ Passou |
| **T08** | API | Endpoint /documents lista PDFs | Manual | ✅ Passou |
| **T09** | Interface | Chat exibe mensagens corretamente | Manual | ✅ Passou |
| **T10** | Interface | Upload de documento funciona | Manual | ✅ Passou |
| **T11** | Interface | Modo escuro alterna corretamente | Manual | ✅ Passou |
| **T12** | Performance | Resposta em < 5 segundos | Manual | ✅ Passou |

## Execução dos Testes

### Testes Automatizados

**Comando:**

```bash
# Executar todos os testes
pytest

# Executar com verbosidade
pytest -v

# Executar testes específicos
pytest tests/test_ingestion.py

# Gerar relatório de cobertura
pytest --cov=app --cov-report=html
```

**Saída esperada:**

```
========================== test session starts ==========================
collected 6 items

tests/test_ingestion.py::test_pdf_loading ✅ PASSED
tests/test_ingestion.py::test_chunking ✅ PASSED
tests/test_ingestion.py::test_embeddings ✅ PASSED
tests/test_ingestion.py::test_vector_search ✅ PASSED
tests/test_ingestion.py::test_database ✅ PASSED
tests/test_ingestion.py::test_integration ✅ PASSED

========================== 6 passed in 12.45s ==========================
```

## Fixtures e Setup

O arquivo `conftest.py` contém fixtures compartilhadas:

```python
@pytest.fixture
def test_pdf_path():
    """Retorna caminho para PDF de teste"""
    return "tests/fixtures/sample.pdf"

@pytest.fixture
def test_database():
    """Setup de banco de dados de teste"""
    # Criar banco temporário
    yield db_connection
    # Cleanup após teste
```

## Desafios e Aprendizados

### Desafios Encontrados

**Testes com IA:**

- Respostas do Gemini não são determinísticas
- Dificulta testes de regressão exatos
- Solução: Validar estrutura e fontes, não conteúdo exato

**Testes com Embeddings:**

- Modelo demora para carregar
- Solução: Cache do modelo entre testes

**Banco de Dados:**

- Isolamento entre testes
- Solução: Fixtures para setup/teardown

### Melhorias Futuras

**Cobertura:**

- Aumentar cobertura de testes unitários
- Adicionar testes no frontend com Vitest
- Testar edge cases e erros

**Automação:**

- Integrar testes com CI/CD no GitHub Actions
- Testes automáticos em cada PR
- Relatórios de cobertura automáticos

**Performance:**

- Adicionar testes de carga
- Validar escalabilidade com muitos documentos
- Medir tempo de resposta sob stress

## Qualidade de Código

Além dos testes, outras práticas garantiram qualidade:

**Type Hints:**

- Todo código Python utiliza type annotations
- Validação estática com mypy (opcional)

**Linting:**

- Code style consistente
- Docstrings em funções importantes

**Code Review:**

- Todos os PRs passam por revisão
- Dois pares de olhos em cada mudança

Essa abordagem de testes garantiu que as funcionalidades principais do sistema, especialmente o pipeline RAG, funcionassem de forma confiável e previsível.
