# Backend - Estrutura e Implementação

## FastAPI: Framework Moderno

### Configuração da Aplicação

A aplicação é inicializada com middlewares e handlers essenciais:

```python
app = FastAPI(
    title="RAGBot",
    version="1.0.0",
    description="Backend API para sistema RAG"
)

# CORS para comunicação com frontend
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"] if debug else ["http://localhost:5173"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"]
)

# Middleware de logging
app.middleware("http")(log_requests)

# Error handlers personalizados
app.exception_handler(404)(not_found_handler)
app.exception_handler(500)(internal_error_handler)
```

### Lifecycle Management

O FastAPI gerencia o ciclo de vida da aplicação:

```python
@asynccontextmanager
async def lifespan(app: FastAPI):
    # Inicialização
    logger.info("Iniciando RAGBot...")
    if not db_manager.test_connection():
        raise RuntimeError("Falha na conexão com banco")
    
    yield  # Aplicação rodando
    
    # Finalização
    logger.info("Finalizando RAGBot...")
```

**Vantagens:**

- Validação de dependências na inicialização
- Falha rápida se configuração estiver incorreta
- Cleanup adequado ao encerrar

## Pydantic: Validação de Dados

### Schemas Tipados

O Pydantic garante que todos os dados de entrada e saída sejam validados:

```python
class ChatRequest(BaseModel):
    message: str = Field(
        ..., 
        min_length=1, 
        max_length=1000,
        description="Mensagem do usuário"
    )
    conversation_id: Optional[UUID] = Field(
        None, 
        description="ID da conversa"
    )
    max_chunks: int = Field(
        ..., 
        ge=1, 
        le=10,
        description="Máximo de chunks a recuperar"
    )

class ChatResponse(BaseModel):
    response: str
    conversation_id: UUID
    message_id: UUID
    sources: List[SourceChunk]
    processing_time: float
```

### Benefícios

**Validação Automática:**

- FastAPI valida requests automaticamente
- Erros 422 com detalhes do que está errado
- Conversão de tipos automática

**Documentação:**

- Schemas aparecem no Swagger UI
- Exemplos de requests/responses gerados
- Tipos e descrições claras

**Type Safety:**

- IDEs oferecem autocompletar
- Erros de tipo detectados antes da execução
- Refatoração segura do código

## Estrutura de Pastas

A organização do código segue o padrão de camadas:

```
app/
├── config/
│   ├── settings.py       # Variáveis de ambiente
│   └── constants.py      # Constantes da aplicação
│
├── routes/
│   ├── core_routes.py    # Health check, info
│   ├── chat_routes.py    # Endpoints de chat
│   └── document_routes.py # Upload e listagem de documentos
│
├── services/
│   ├── chat_service.py   # Lógica do RAG
│   └── document_service.py # Processamento de documentos
│
├── repositories/
│   ├── chat_repository.py      # Operações de conversas
│   ├── document_repository.py  # Operações de documentos
│   └── vector_repository.py    # Busca vetorial
│
├── schemas/
│   ├── chat_schemas.py         # Modelos de chat
│   ├── document_schemas.py     # Modelos de documentos
│   └── shared_schemas.py       # Modelos compartilhados
│
├── application.py        # Factory da aplicação
└── main.py              # Entry point
```

## Camadas e Responsabilidades

### 1. Routes (Endpoints)

**Responsabilidade:** Receber requisições HTTP e retornar respostas.

```python
@router.post("/ask", response_model=ChatResponse)
async def ask_question(request: ChatRequest):
    """Endpoint para enviar perguntas ao chatbot."""
    service = ChatService()
    response = await service.process_chat(
        user_message=request.message,
        max_chunks=request.max_chunks,
        conversation_id=request.conversation_id
    )
    return response
```

**Características:**

- Validação de entrada via Pydantic
- Chamadas assíncronas para não bloquear
- Documentação via docstrings
- Status codes HTTP apropriados

### 2. Services (Lógica de Negócio)

**Responsabilidade:** Implementar a lógica principal do sistema.

```python
class ChatService:
    def __init__(self):
        self.model = genai.GenerativeModel('gemini-2.5-flash')
        self.vector_store = get_vector_store()
        self.chat_repository = ChatRepository()
    
    async def process_chat(self, user_message: str, 
                          max_chunks: int) -> ChatResponse:
        # 1. Buscar chunks relevantes
        chunks = self.vector_store.similarity_search(
            user_message, k=max_chunks
        )
        
        # 2. Construir prompt
        prompt = self._build_prompt(user_message, chunks)
        
        # 3. Gerar resposta com Gemini
        response = self.model.generate_content(prompt)
        
        # 4. Salvar no banco
        self.chat_repository.save_message(...)
        
        return ChatResponse(...)
```

**Características:**

- Orquestração de múltiplos componentes
- Lógica de negócio isolada
- Fácil de testar unitariamente
- Sem dependência direta do FastAPI

### 3. Repositories (Acesso a Dados)

**Responsabilidade:** Interagir com banco de dados e vector store.

```python
class ChatRepository:
    def create_conversation(self) -> UUID:
        """Cria nova conversa no banco."""
        query = "INSERT INTO conversations DEFAULT VALUES RETURNING id"
        result = db_manager.execute_query(query)
        return result[0]['id']
    
    def save_message(self, conversation_id: UUID, 
                     role: str, content: str) -> UUID:
        """Salva mensagem no banco."""
        query = """
            INSERT INTO messages (conversation_id, role, content)
            VALUES (%s, %s, %s) RETURNING id
        """
        result = db_manager.execute_query(
            query, (conversation_id, role, content)
        )
        return result[0]['id']
```

**Características:**

- Abstração do acesso a dados
- Queries SQL isoladas
- Facilita troca de banco de dados
- Testável com mock do banco

### 4. Schemas (Modelos de Dados)

**Responsabilidade:** Definir contratos de entrada e saída.

```python
class SourceChunk(BaseModel):
    content: str = Field(..., description="Trecho do documento")
    document_name: str = Field(..., description="Nome do documento")
    page_number: Optional[int] = Field(None, description="Número da página")
    similarity_score: float = Field(..., description="Score de similaridade")
```

**Características:**

- Reutilizáveis entre diferentes endpoints
- Validação consistente em todo sistema
- Documentação centralizada
- Type hints para toda aplicação

## Boas Práticas Implementadas

### Separation of Concerns

Cada camada tem responsabilidade bem definida:

- **Routes**: HTTP apenas
- **Services**: Lógica de negócio
- **Repositories**: Persistência
- **Schemas**: Validação

### Dependency Injection

FastAPI facilita injeção de dependências:

```python
def get_chat_service() -> ChatService:
    return ChatService()

@router.post("/ask")
async def ask_question(
    request: ChatRequest,
    service: ChatService = Depends(get_chat_service)
):
    return await service.process_chat(...)
```

### Logging Estruturado

Toda operação importante é registrada:

```python
logger.info(f"Processing chat request: {user_message[:50]}...")
logger.info(f"Found {len(chunks)} relevant chunks")
logger.success(f"Response generated in {elapsed:.2f}s")
logger.error(f"Error processing chat: {e}")
```

**Benefícios:**

- Debug facilitado em produção
- Auditoria de operações
- Monitoramento de performance
- Rastreamento de erros

### Error Handling Centralizado

Erros são tratados consistentemente:

```python
@app.exception_handler(404)
async def not_found_handler(request: Request, exc):
    return JSONResponse(
        status_code=404,
        content={
            "error": "Endpoint não encontrado",
            "detail": f"O endpoint {request.url.path} não existe"
        }
    )
```

### Type Hints Completos

Todo o código usa type annotations:

```python
def similarity_search(
    self, 
    query: str, 
    k: int = 5
) -> List[Dict[str, Any]]:
    ...
```

**Vantagens:**

- IDEs oferecem autocompletar melhor
- Detecção de erros em tempo de desenvolvimento
- Documentação implícita do código
- Facilita refatoração

### Configuração via Ambiente

Todas as configurações são externalizadas:

```python
class Settings(BaseSettings):
    database_url: str
    gemini_api_key: str
    debug: bool = False
    log_level: str = "INFO"
    
    class Config:
        env_file = ".env"
```

**Benefícios:**

- Diferentes configs por ambiente (dev/prod)
- Secrets não ficam no código
- Fácil deploy em containers
- Configuração centralizada

## Performance e Escalabilidade

### Operações Assíncronas

FastAPI permite I/O não bloqueante:

```python
async def process_chat(...):
    # Não bloqueia thread enquanto espera BD
    chunks = await self.vector_store.similarity_search(...)
    
    # Não bloqueia thread enquanto espera Gemini
    response = await self.model.generate_content(...)
```

### Modularidade

A arquitetura permite escalar componentes independentemente:

- Services podem virar microserviços
- Repositories podem usar cache
- Routes podem ter rate limiting
- Fácil adicionar novos endpoints

### Testabilidade

Separação de camadas facilita testes:

- Unit tests nos Services
- Integration tests nos Repositories
- E2E tests nas Routes
- Mock de dependências externas
