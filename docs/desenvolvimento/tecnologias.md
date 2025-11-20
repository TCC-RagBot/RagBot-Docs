# Stack Tecnológica - RAGBot

## 🎯 Visão Geral das Tecnologias

O RAGBot foi desenvolvido utilizando tecnologias modernas e robustas, priorizando **performance**, **escalabilidade** e **maintibilidade**. A stack foi cuidadosamente selecionada para garantir uma experiência de desenvolvimento produtiva e um sistema confiável em produção.

---

## 🐍 Backend Technologies

### Python 3.11+
![Python](https://img.shields.io/badge/Python-3.11+-3776ab?style=for-the-badge&logo=python&logoColor=white)

**Por que Python 3.11?**
- ✅ **Performance melhorada** em 10-60% comparado ao 3.10
- ✅ **Exception Groups** para melhor tratamento de erros
- ✅ **Tomllib** integrado para configurações TOML
- ✅ **Type annotations** aprimoradas
- ✅ **Ecossistema maduro** para IA/ML

**Funcionalidades Utilizadas:**
```python
# Async/await nativo
async def process_chat(message: str) -> ChatResponse:
    embedding = await generate_embedding(message)
    chunks = await search_similar_chunks(embedding)
    return await generate_response(chunks)

# Type hints modernos
from typing import Optional, List, Union
from uuid import UUID

def validate_document(doc_id: UUID) -> Optional[Document]:
    ...
```

---

### FastAPI 0.115+
![FastAPI](https://img.shields.io/badge/FastAPI-0.115+-009688?style=for-the-badge&logo=fastapi&logoColor=white)

**Vantagens do FastAPI:**
- 🚀 **Performance excepcional** (comparable ao NodeJS)
- 📚 **Documentação automática** com Swagger/OpenAPI
- 🔒 **Validação automática** com Pydantic
- ⚡ **Async/await nativo** para operações I/O
- 🛡️ **Security features** integradas

**Configuração Atual:**
```python
# application.py
app = FastAPI(
    title="RAGBot API",
    description="Sistema inteligente de chat com RAG",
    version="1.0.0",
    docs_url="/docs" if settings.debug else None,
    redoc_url="/redoc" if settings.debug else None
)

# Middleware configurado
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.allowed_origins,
    allow_credentials=True,
    allow_methods=["GET", "POST", "DELETE"],
    allow_headers=["*"]
)
```

**Features Implementadas:**
- ✅ **Automatic API docs** (/docs, /redoc)
- ✅ **Request/Response validation** 
- ✅ **Error handling middleware**
- ✅ **CORS configuration**
- ✅ **Logging middleware**
- ✅ **Health check endpoints**

---

### Pydantic 2.10+
![Pydantic](https://img.shields.io/badge/Pydantic-2.10+-e92063?style=for-the-badge)

**Benefícios da Validação:**
- 🔍 **Type validation** automática
- 🛠️ **Data serialization** otimizada
- 📝 **Schema generation** para OpenAPI
- 🧹 **Data sanitization** automática

**Schemas Implementados:**
```python
# chat_schemas.py
class ChatRequest(BaseModel):
    message: str = Field(..., min_length=1, max_length=1000)
    conversation_id: Optional[UUID] = None
    max_chunks: int = Field(5, ge=1, le=10)
    
    model_config = ConfigDict(
        json_schema_extra={
            "example": {
                "message": "O que é pênalti?",
                "max_chunks": 5
            }
        }
    )
```

---

### LangChain 0.3.7
![LangChain](https://img.shields.io/badge/LangChain-0.3.7-1c3c3c?style=for-the-badge)

**Orquestração de IA:**
- 🔗 **Chain abstractions** para fluxos complexos
- 📄 **Document processing** integrado
- 🧮 **Vector store integration**
- 🤖 **LLM abstraction layer**

**Implementação no RAGBot:**
```python
# Configuração do Vector Store
from langchain_postgres import PGVector
from langchain_community.embeddings import SentenceTransformerEmbeddings

embeddings = SentenceTransformerEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)

vector_store = PGVector(
    collection_name="ragbot_chunks",
    connection_string=settings.database_url,
    embedding_function=embeddings
)

# Processamento de documentos
from langchain.text_splitter import RecursiveCharacterTextSplitter

text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    separators=["\n\n", "\n", ". ", " ", ""]
)
```

---

### PostgreSQL 15+ com pgvector
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-316192?style=for-the-badge&logo=postgresql&logoColor=white)

**Banco de Dados Principal:**
- 🗄️ **ACID compliance** para consistência
- 🔍 **Full-text search** nativo
- 📊 **JSON support** para dados flexíveis
- ⚡ **Performance otimizada** com índices

**pgvector Extension:**
```sql
-- Instalação e configuração
CREATE EXTENSION IF NOT EXISTS vector;

-- Tabela de embeddings
CREATE TABLE embeddings (
    id UUID PRIMARY KEY,
    chunk_id UUID REFERENCES chunks(id),
    embedding_vector vector(384),  -- all-MiniLM-L6-v2
    model_name VARCHAR(100),
    created_at TIMESTAMP DEFAULT NOW()
);

-- Índice HNSW para busca eficiente
CREATE INDEX ON embeddings 
USING hnsw (embedding_vector vector_cosine_ops);
```

**Vantagens do pgvector:**
- 🚀 **Busca vetorial rápida** com algoritmo HNSW
- 📏 **Múltiplas métricas** (cosine, L2, inner product)
- 🔧 **Integração nativa** com PostgreSQL
- 📈 **Escalabilidade** para milhões de vetores

---

## 🎨 Frontend Technologies

### Vue.js 3.5+
![Vue.js](https://img.shields.io/badge/Vue.js-3.5+-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white)

**Composition API:**
```typescript
// chatStore.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useChatStore = defineStore('chat', () => {
  const messages = ref<Message[]>([])
  const isTyping = ref(false)
  const currentTheme = ref<'light' | 'dark'>('light')
  
  const lastMessage = computed(() => 
    messages.value[messages.value.length - 1]
  )
  
  async function sendMessage(content: string) {
    isTyping.value = true
    try {
      const response = await apiService.sendMessage(content)
      addMessage('assistant', response.answer)
    } finally {
      isTyping.value = false
    }
  }
  
  return { messages, isTyping, sendMessage, lastMessage }
})
```

**Features Implementadas:**
- ✅ **Composition API** para lógica reativa
- ✅ **TypeScript integration** completa
- ✅ **Component modularity** com props tipadas
- ✅ **Reactivity system** otimizado
- ✅ **Lifecycle hooks** para gerenciamento de estado

---

### TypeScript 5.9+
![TypeScript](https://img.shields.io/badge/TypeScript-5.9+-3178c6?style=for-the-badge&logo=typescript&logoColor=white)

**Type Safety Completa:**
```typescript
// Interfaces bem definidas
interface ChatMessage {
  id: string
  content: string
  role: 'user' | 'assistant'
  timestamp: Date
  sources?: SourceChunk[]
}

interface SourceChunk {
  content: string
  document_id: string
  chunk_index: number
  similarity: number
}

// API service tipado
class ApiService {
  async sendMessage(
    question: string, 
    conversationId?: string, 
    maxChunks: number = 5
  ): Promise<ChatResponse> {
    // Implementation...
  }
}
```

**Benefícios:**
- 🛡️ **Detecção de erros** em tempo de desenvolvimento
- 📝 **IntelliSense** avançado no VS Code
- 🔧 **Refactoring seguro** com rename automático
- 📚 **Self-documenting code** com tipos descritivos

---

### Vite 7.1+
![Vite](https://img.shields.io/badge/Vite-7.1+-646CFF?style=for-the-badge&logo=vite&logoColor=white)

**Build Tool Moderna:**
```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true
      }
    }
  },
  build: {
    target: 'esnext',
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['vue', 'vue-router', 'pinia'],
          ui: ['marked']
        }
      }
    }
  }
})
```

**Vantagens:**
- ⚡ **Hot Module Replacement** instantâneo
- 📦 **Bundle otimizado** com tree-shaking
- 🔧 **Zero-config** para projetos Vue
- 🚀 **Build rápido** com esbuild

---

### Tailwind CSS 3.4+
![Tailwind](https://img.shields.io/badge/Tailwind%20CSS-3.4+-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)

**Utility-First CSS:**
```html
<!-- ChatBubble.vue -->
<div class="flex items-start space-x-3 max-w-4xl mx-auto">
  <div class="flex-shrink-0">
    <div class="w-8 h-8 rounded-full bg-blue-500 flex items-center justify-center">
      <ChatBubbleLeftIcon class="w-4 h-4 text-white" />
    </div>
  </div>
  <div class="flex-1 min-w-0">
    <div class="bg-white dark:bg-gray-800 rounded-lg px-4 py-3 shadow-sm border border-gray-200 dark:border-gray-700">
      <div class="prose prose-sm dark:prose-invert max-w-none" v-html="formattedContent"></div>
    </div>
  </div>
</div>
```

**Configuração Personalizada:**
```javascript
// tailwind.config.js
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts}'],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8'
        }
      },
      typography: {
        DEFAULT: {
          css: {
            maxWidth: 'none',
            color: 'inherit'
          }
        }
      }
    }
  },
  plugins: [
    require('@tailwindcss/typography'),
    require('@tailwindcss/forms')
  ]
}
```

---

### Pinia 3.0+
![Pinia](https://img.shields.io/badge/Pinia-3.0+-f7d336?style=for-the-badge)

**State Management Moderno:**
```typescript
// documentStore.ts
export const useDocumentStore = defineStore('documents', () => {
  const documents = ref<Document[]>([])
  const isUploading = ref(false)
  const uploadProgress = ref(0)
  
  const totalDocuments = computed(() => documents.value.length)
  const totalSize = computed(() => 
    documents.value.reduce((sum, doc) => sum + doc.file_size, 0)
  )
  
  async function uploadDocument(file: File): Promise<void> {
    isUploading.value = true
    uploadProgress.value = 0
    
    try {
      const formData = new FormData()
      formData.append('file', file)
      
      const response = await fetch('/api/documents/upload', {
        method: 'POST',
        body: formData
      })
      
      if (response.ok) {
        await loadDocuments()
      }
    } finally {
      isUploading.value = false
      uploadProgress.value = 0
    }
  }
  
  return {
    documents,
    isUploading,
    uploadProgress,
    totalDocuments,
    totalSize,
    uploadDocument
  }
})
```

---

## 🤖 IA & Machine Learning

### Google Gemini AI
![Google](https://img.shields.io/badge/Google%20Gemini-AI-4285f4?style=for-the-badge&logo=google&logoColor=white)

**Large Language Model:**
```python
# Configuração do Gemini
import google.generativeai as genai

genai.configure(api_key=settings.gemini_api_key)

model = genai.GenerativeModel(
    model_name="gemini-1.5-flash",
    generation_config={
        "temperature": 0.1,  # Respostas mais precisas
        "max_output_tokens": 1000,
        "top_p": 0.8
    }
)

# Prompt engineering otimizado
SYSTEM_PROMPT = """
Você é um assistente especializado em responder perguntas baseado 
EXCLUSIVAMENTE no contexto fornecido. Regras importantes:

1. Use APENAS as informações dos documentos fornecidos
2. Se a resposta não estiver no contexto, diga claramente
3. Seja preciso e direto
4. Cite as fontes quando relevante
"""
```

**Vantagens do Gemini:**
- 🧠 **Compreensão contextual** avançada
- 💰 **Custo-benefício** excelente
- 🚀 **Velocidade** de resposta otimizada
- 🌍 **Suporte multilíngue** nativo

---

### sentence-transformers 3.3.1
![Transformers](https://img.shields.io/badge/sentence--transformers-3.3.1-ff6f00?style=for-the-badge)

**Geração de Embeddings:**
```python
from sentence_transformers import SentenceTransformer

# Modelo otimizado para português e inglês
model = SentenceTransformer('all-MiniLM-L6-v2')

def generate_embedding(text: str) -> List[float]:
    """
    Gera embedding de 384 dimensões para o texto.
    
    Args:
        text: Texto para gerar embedding
        
    Returns:
        Lista de floats representando o vetor
    """
    embedding = model.encode(text, normalize_embeddings=True)
    return embedding.tolist()

# Busca por similaridade
def semantic_search(query: str, chunks: List[str], top_k: int = 5):
    query_embedding = model.encode([query])
    chunk_embeddings = model.encode(chunks)
    
    similarities = cosine_similarity(query_embedding, chunk_embeddings)[0]
    top_indices = similarities.argsort()[-top_k:][::-1]
    
    return [(chunks[i], similarities[i]) for i in top_indices]
```

**Características do all-MiniLM-L6-v2:**
- 📏 **384 dimensões** (eficiente)
- 🌐 **Multilíngue** (50+ idiomas)
- ⚡ **Rápido** (< 50ms por encoding)
- 🎯 **Alta precisão** para busca semântica

---

### PyPDF 5.1.0
![PDF](https://img.shields.io/badge/PyPDF-5.1.0-red?style=for-the-badge)

**Processamento de PDFs:**
```python
import pypdf

def extract_pdf_content(pdf_bytes: bytes) -> str:
    """
    Extrai texto de um arquivo PDF.
    
    Args:
        pdf_bytes: Conteúdo binário do PDF
        
    Returns:
        Texto extraído do PDF
        
    Raises:
        ValueError: Se o PDF for inválido
    """
    try:
        pdf_reader = pypdf.PdfReader(io.BytesIO(pdf_bytes))
        
        if len(pdf_reader.pages) == 0:
            raise ValueError("PDF não possui páginas")
        
        text_content = []
        for page in pdf_reader.pages:
            page_text = page.extract_text()
            if page_text.strip():
                text_content.append(page_text.strip())
        
        full_text = "\n\n".join(text_content)
        
        if len(full_text.strip()) < 50:
            raise ValueError("PDF possui muito pouco texto extraível")
        
        return full_text
    
    except Exception as e:
        raise ValueError(f"Erro ao processar PDF: {str(e)}")
```

---

## 🔧 DevOps & Infrastructure

### Docker & Docker Compose
![Docker](https://img.shields.io/badge/Docker-Containerization-2496ed?style=for-the-badge&logo=docker&logoColor=white)

**PostgreSQL Container:**
```yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: pgvector/pgvector:pg15
    environment:
      POSTGRES_DB: ragbot_db
      POSTGRES_USER: tccrag
      POSTGRES_PASSWORD: tcc123
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql
    command: postgres -c shared_preload_libraries=vector

volumes:
  postgres_data:
```

---

### Testing Framework

#### Backend Testing - pytest
```python
# test_chat_service.py
import pytest
from app.services.chat_service import chat_service

@pytest.mark.asyncio
async def test_process_chat_success():
    response = await chat_service.process_chat(
        user_message="Teste de pergunta",
        max_chunks=3
    )
    
    assert response.response is not None
    assert response.processing_time > 0
    assert len(response.sources) <= 3
```

#### Frontend Testing - Vitest
```typescript
// App.spec.ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import ChatBubble from '@/modules/chat/components/ChatBubble.vue'

describe('ChatBubble', () => {
  it('renders user message correctly', () => {
    const wrapper = mount(ChatBubble, {
      props: {
        message: 'Test message',
        isUser: true
      }
    })
    
    expect(wrapper.text()).toContain('Test message')
    expect(wrapper.classes()).toContain('user-message')
  })
})
```

---

## 🚀 Performance e Otimizações

### Backend Optimizations
- ✅ **Async/await** para operações I/O
- ✅ **Connection pooling** no banco
- ✅ **Caching** de embeddings calculados
- ✅ **Índices otimizados** no PostgreSQL
- ✅ **Pydantic V2** para validação rápida

### Frontend Optimizations
- ✅ **Code splitting** automático
- ✅ **Lazy loading** de rotas
- ✅ **Bundle optimization** com Vite
- ✅ **Tree shaking** para código não utilizado
- ✅ **CSS purging** no build de produção

### Database Optimizations
```sql
-- Índices para performance
CREATE INDEX CONCURRENTLY idx_chunks_document_id 
ON chunks(document_id);

CREATE INDEX CONCURRENTLY idx_embeddings_hnsw 
ON embeddings USING hnsw (embedding_vector vector_cosine_ops);

-- Configurações de performance
ALTER SYSTEM SET shared_buffers = '256MB';
ALTER SYSTEM SET effective_cache_size = '1GB';
ALTER SYSTEM SET maintenance_work_mem = '64MB';
```

---

## 📊 Métricas e Monitoramento

### Logging com Loguru
```python
from loguru import logger

# Configuração estruturada
logger.configure(
    handlers=[
        {
            "sink": sys.stdout,
            "format": "<green>{time:YYYY-MM-DD HH:mm:ss}</green> | "
                     "<level>{level: <8}</level> | "
                     "<cyan>{name}</cyan>:<cyan>{function}</cyan>:<cyan>{line}</cyan> - "
                     "<level>{message}</level>",
            "level": "INFO"
        }
    ]
)

# Logs contextuais
@logger.catch
async def process_chat(message: str):
    start_time = time.time()
    logger.info(f"Processing chat: {message[:100]}...")
    
    try:
        result = await _process_internal(message)
        duration = time.time() - start_time
        logger.success(f"Chat processed in {duration:.2f}s")
        return result
    except Exception as e:
        logger.error(f"Chat processing failed: {e}")
        raise
```

---

## 🔮 Roadmap Tecnológico

### Próximas Versões

**v1.1 - Streaming & Real-time**
- 🔄 **Server-Sent Events** para streaming
- ⚡ **WebSockets** para chat em tempo real
- 📊 **Real-time metrics** dashboard

**v1.2 - Observability**
- 📈 **Prometheus** metrics
- 🔍 **Distributed tracing** com OpenTelemetry
- 📊 **Grafana** dashboards

**v1.3 - Advanced AI**
- 🧠 **Multiple LLM** support (Claude, GPT-4)
- 🔄 **Model switching** por conversação
- 🎯 **Fine-tuned embeddings** para domínio específico

**v2.0 - Microservices**
- 🏗️ **Service mesh** architecture
- 📦 **Kubernetes** deployment
- 🔐 **OAuth 2.0** authentication
- 🌐 **Multi-tenant** support

---

!!! success "Stack Moderna e Escalável"
    A stack tecnológica do RAGBot foi escolhida para garantir **alta performance**, **developer experience** excepcional e **facilidade de manutenção**. Todas as tecnologias são amplamente adotadas pela indústria e possuem comunidades ativas.

**Última atualização:** 19 de novembro de 2025

Lista e justificativa das tecnologias, frameworks e bibliotecas utilizadas para construir o RagBot.
