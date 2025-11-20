# API Reference - RAGBot

## 🔌 Base URL e Configuração

### Ambiente de Desenvolvimento
```
Base URL: http://localhost:8000
```

### Ambientes de Produção
```
Production: https://ragbot-api.exemplo.com
Staging: https://staging-ragbot-api.exemplo.com
```

### 📊 Documentação Interativa
- **Swagger UI**: `http://localhost:8000/docs`
- **ReDoc**: `http://localhost:8000/redoc`
- **OpenAPI JSON**: `http://localhost:8000/openapi.json`

---

## 🛡️ Autenticação e Headers

### Headers Obrigatórios
```http
Content-Type: application/json
Accept: application/json
```

### Headers Opcionais
```http
X-Request-ID: uuid-opcional-para-tracking
User-Agent: RagBot-Client/1.0.0
```

!!! info "Autenticação"
    A versão atual **não requer autenticação**, mas futuras versões incluirão:
    
    - API Keys para acesso programático
    - JWT tokens para usuários autenticados
    - Rate limiting por IP/usuário

---

## 🏠 Core Endpoints

### GET `/` - Welcome Message

Endpoint de boas-vindas que fornece informações básicas da API.

**Requisição:**
```http
GET /
```

**Resposta 200:**
```json
{
  "message": "Bem-vindo ao RAGBot!",
  "version": "1.0.0",
  "docs": "/docs"
}
```

---

### GET `/health` - Health Check

Endpoint para monitoramento da saúde do sistema, incluindo status do banco de dados.

**Requisição:**
```http
GET /health
```

**Resposta 200 - Sistema Saudável:**
```json
{
  "status": "healthy",
  "timestamp": "2025-11-19T10:30:00.000Z",
  "version": "1.0.0",
  "database_status": "healthy"
}
```

**Resposta 200 - Sistema com Problemas:**
```json
{
  "status": "degraded",
  "timestamp": "2025-11-19T10:30:00.000Z",
  "version": "1.0.0",
  "database_status": "unhealthy"
}
```

**Status Possíveis:**
- `healthy`: Sistema funcionando perfeitamente
- `degraded`: Sistema parcialmente funcional
- `unhealthy`: Sistema com problemas críticos

---

## 💬 Chat Endpoints

### POST `/api/chat` - Enviar Mensagem

Endpoint principal para interação com o RAGBot. Processa a mensagem do usuário e retorna uma resposta baseada nos documentos carregados.

**Requisição:**
```http
POST /api/chat
Content-Type: application/json

{
  "message": "O que é pênalti no futebol?",
  "conversation_id": "550e8400-e29b-41d4-a716-446655440000",
  "max_chunks": 5
}
```

**Parâmetros do Body:**

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `message` | string | ✅ | Mensagem/pergunta do usuário (1-1000 caracteres) |
| `conversation_id` | uuid | ❌ | ID da conversa (cria nova se omitido) |
| `max_chunks` | integer | ✅ | Número máximo de chunks para contexto (1-10) |

**Resposta 200 - Sucesso:**
```json
{
  "response": "Um pênalti no futebol é uma infração cometida dentro da área penal que resulta em um tiro livre direto...",
  "conversation_id": "550e8400-e29b-41d4-a716-446655440000",
  "message_id": "660f9500-f30c-42e5-b827-556766551111",
  "sources": [
    {
      "content": "Pênalti é uma infração cometida dentro da área penal...",
      "document_id": "770fa600-040d-43f6-c938-667877662222",
      "chunk_index": 15,
      "similarity": 0.89
    }
  ],
  "processing_time": 2.45
}
```

**Campos da Resposta:**

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `response` | string | Resposta gerada pelo RAGBot |
| `conversation_id` | uuid | ID único da conversa |
| `message_id` | uuid | ID único da mensagem |
| `sources` | array | Lista de chunks de documentos utilizados |
| `processing_time` | float | Tempo de processamento em segundos |

**Códigos de Erro:**

=== "400 Bad Request"
    ```json
    {
      "detail": "Mensagem deve ter entre 1 e 1000 caracteres"
    }
    ```

=== "422 Validation Error"
    ```json
    {
      "detail": [
        {
          "loc": ["body", "max_chunks"],
          "msg": "ensure this value is greater than or equal to 1",
          "type": "value_error.number.not_ge"
        }
      ]
    }
    ```

=== "500 Internal Server Error"
    ```json
    {
      "detail": "Erro ao processar chat: Database connection failed"
    }
    ```

---

### GET `/api/conversations/{conversation_id}/messages` - Histórico

**🚧 Em Desenvolvimento**

Endpoint para recuperar o histórico de mensagens de uma conversa.

**Requisição:**
```http
GET /api/conversations/550e8400-e29b-41d4-a716-446655440000/messages
```

**Resposta 200:**
```json
{
  "conversation_id": "550e8400-e29b-41d4-a716-446655440000",
  "messages": [],
  "message": "Endpoint em desenvolvimento"
}
```

---

## 📄 Document Endpoints

### GET `/api/documents/list` - Listar Documentos

Lista todos os documentos processados no sistema.

**Requisição:**
```http
GET /api/documents/list
```

**Resposta 200:**
```json
{
  "documents": [
    {
      "id": "770fa600-040d-43f6-c938-667877662222",
      "filename": "manual-futebol.pdf",
      "content_type": "application/pdf",
      "file_size": 1024000,
      "chunk_count": 45,
      "created_at": "2025-11-19T09:15:30.000Z"
    },
    {
      "id": "880fb700-151e-44g7-d049-778988773333",
      "filename": "regras-basketball.pdf",
      "content_type": "application/pdf",
      "file_size": 2048000,
      "chunk_count": 72,
      "created_at": "2025-11-18T14:22:45.000Z"
    }
  ],
  "total_documents": 2,
  "total_file_size": 3072000
}
```

**Códigos de Erro:**

=== "500 Internal Server Error"
    ```json
    {
      "detail": "Erro ao listar documentos: Database error"
    }
    ```

---

### POST `/api/documents/upload` - Upload de Documento

Faz upload e processa um novo documento PDF.

**Requisição:**
```http
POST /api/documents/upload
Content-Type: multipart/form-data

file: [BINARY PDF DATA]
```

**Validações:**
- ✅ Arquivo deve ter extensão `.pdf`
- ✅ Arquivo não pode estar vazio
- ✅ Nome do arquivo é obrigatório
- ✅ Conteúdo deve ser um PDF válido

**Resposta 200 - Sucesso:**
```json
{
  "status": "success",
  "message": "Documento processado com sucesso",
  "document_id": "990fc800-262f-45h8-e150-889099884444",
  "filename": "novo-documento.pdf",
  "chunks_created": 38,
  "processing_time": 4.23,
  "file_size": 1536000
}
```

**Códigos de Erro:**

=== "400 Bad Request - Nome obrigatório"
    ```json
    {
      "detail": "Nome do arquivo é obrigatório"
    }
    ```

=== "400 Bad Request - Apenas PDF"
    ```json
    {
      "detail": "Apenas arquivos PDF são suportados"
    }
    ```

=== "400 Bad Request - Arquivo vazio"
    ```json
    {
      "detail": "Arquivo está vazio"
    }
    ```

=== "422 Unprocessable Entity"
    ```json
    {
      "detail": "error: PDF inválido ou corrompido"
    }
    ```

=== "500 Internal Server Error"
    ```json
    {
      "detail": "Erro interno ao processar documento: Unexpected error"
    }
    ```

---

### DELETE `/api/documents/{document_id}` - Excluir Documento

Remove um documento e todos os seus chunks/embeddings do sistema.

**Requisição:**
```http
DELETE /api/documents/770fa600-040d-43f6-c938-667877662222
```

**Resposta 200:**
```json
{
  "status": "success",
  "message": "Documento excluído com sucesso",
  "filename": "manual-futebol.pdf",
  "chunks_deleted": 45
}
```

**Códigos de Erro:**

=== "404 Not Found"
    ```json
    {
      "detail": "Documento não encontrado"
    }
    ```

=== "500 Internal Server Error"
    ```json
    {
      "detail": "Erro ao excluir documento: Database error"
    }
    ```

---

## 📊 Schemas e Modelos

### ChatRequest
```python
{
  "message": str,          # 1-1000 caracteres
  "conversation_id": UUID, # Opcional
  "max_chunks": int        # 1-10
}
```

### ChatResponse
```python
{
  "response": str,
  "conversation_id": UUID,
  "message_id": UUID,
  "sources": List[SourceChunk],
  "processing_time": float
}
```

### SourceChunk
```python
{
  "content": str,
  "document_id": UUID,
  "chunk_index": int,
  "similarity": float  # 0.0-1.0
}
```

### DocumentUploadResponse
```python
{
  "status": str,
  "message": str,
  "document_id": UUID,
  "filename": str,
  "chunks_created": int,
  "processing_time": float,
  "file_size": int
}
```

### DocumentListResponse
```python
{
  "documents": List[DocumentInfo],
  "total_documents": int,
  "total_file_size": int
}
```

### HealthResponse
```python
{
  "status": str,           # "healthy" | "degraded" | "unhealthy"
  "timestamp": str,        # ISO 8601
  "version": str,
  "database_status": str   # "healthy" | "unhealthy" | "error"
}
```

---

## 🔄 Códigos de Status HTTP

| Código | Significado | Uso |
|--------|-------------|-----|
| **200** | OK | Operação realizada com sucesso |
| **400** | Bad Request | Dados de entrada inválidos |
| **404** | Not Found | Recurso não encontrado |
| **422** | Unprocessable Entity | Erro de validação ou processamento |
| **500** | Internal Server Error | Erro interno do servidor |

---

## 🧪 Exemplos de Uso

### Python com requests
```python
import requests

# Chat básico
response = requests.post('http://localhost:8000/api/chat', json={
    'message': 'O que é pênalti?',
    'max_chunks': 5
})
data = response.json()
print(data['response'])

# Upload de documento
with open('documento.pdf', 'rb') as f:
    response = requests.post(
        'http://localhost:8000/api/documents/upload',
        files={'file': f}
    )
print(response.json())
```

### JavaScript/TypeScript
```javascript
// Chat
const chatResponse = await fetch('http://localhost:8000/api/chat', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    message: 'O que é pênalti?',
    max_chunks: 5
  })
});
const data = await chatResponse.json();

// Upload
const formData = new FormData();
formData.append('file', fileInput.files[0]);
const uploadResponse = await fetch('http://localhost:8000/api/documents/upload', {
  method: 'POST',
  body: formData
});
```

### cURL
```bash
# Chat
curl -X POST "http://localhost:8000/api/chat" \
  -H "Content-Type: application/json" \
  -d '{"message": "O que é pênalti?", "max_chunks": 5}'

# Upload
curl -X POST "http://localhost:8000/api/documents/upload" \
  -F "file=@documento.pdf"

# Health check
curl "http://localhost:8000/health"
```

---

## 🎯 Rate Limiting

!!! warning "Rate Limiting (Futuro)"
    Versões futuras incluirão rate limiting:
    
    - **Chat**: 10 requests/minuto por IP
    - **Upload**: 5 uploads/hora por IP
    - **Listagem**: 100 requests/minuto por IP

---

## 🔍 Monitoramento e Logs

### Logs Estruturados
A API gera logs estruturados para todas as operações:

```json
{
  "timestamp": "2025-11-19T10:30:00.000Z",
  "level": "INFO",
  "message": "Processing chat request: O que é pênalti?...",
  "request_id": "req_123456",
  "processing_time": 2.45,
  "user_ip": "192.168.1.1"
}
```

### Métricas Disponíveis
- ⏱️ **Tempo de resposta** por endpoint
- 📊 **Taxa de erro** por código de status
- 📈 **Throughput** de requests por segundo
- 💾 **Uso de recursos** (CPU, memória, banco)

---

## 🚀 WebSockets (Roadmap)

### Futuras Implementações
```javascript
// Streaming de respostas (v1.1)
const ws = new WebSocket('ws://localhost:8000/ws/chat');
ws.onmessage = (event) => {
  const chunk = JSON.parse(event.data);
  appendToResponse(chunk.content);
};
```

---

!!! success "API Pronta para Produção"
    A API do RAGBot está totalmente funcional, documentada e pronta para integração com qualquer frontend ou sistema cliente.

**Última atualização:** 19 de novembro de 2025

Documentação dos endpoints da API, contratos de requisição/resposta e exemplos de uso.
