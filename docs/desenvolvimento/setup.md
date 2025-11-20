# Setup do Ambiente de Desenvolvimento

## 🎯 Visão Geral

Este guia fornece instruções completas para configurar o ambiente de desenvolvimento do RAGBot, incluindo backend (FastAPI) e frontend (Vue.js). Siga os passos na ordem apresentada para garantir uma configuração correta.

!!! warning "Pré-requisitos Obrigatórios"
    - 🐍 **Python 3.11** (OBRIGATÓRIO - não use 3.12+)
    - 🟢 **Node.js 20.19+ ou 22.12+**
    - 🐳 **Docker & Docker Compose**
    - 🔑 **Google Gemini API Key**
    - 🖥️ **VS Code** (recomendado)

---

## 🚀 Setup Rápido (Quickstart)

### 1. Clone dos Repositórios
```bash
# Crie uma pasta principal para o projeto
mkdir ragbot-project
cd ragbot-project

# Clone o backend
git clone https://github.com/TCC-RagBot/RagBot-Back.git
cd RagBot-Back

# Clone o frontend (em outra aba/janela)
git clone https://github.com/TCC-RagBot/RagBot-Front.git
cd RagBot-Front

# Clone a documentação (opcional)
git clone https://github.com/TCC-RagBot/RagBot-Docs.git
cd RagBot-Docs
```

### 2. Configuração Backend (5 min)
```bash
cd RagBot-Back

# Verificar versão do Python (DEVE ser 3.11.x)
python --version

# Criar e ativar ambiente virtual
python -m venv venv
.\venv\Scripts\Activate.ps1  # PowerShell
# ou: venv\Scripts\activate.bat  # CMD

# Instalar dependências
pip install --upgrade pip
pip install -r requirements.txt

# Configurar variáveis de ambiente
copy .env.example .env
# Editar .env com sua API key do Gemini

# Subir banco PostgreSQL
docker-compose up -d

# Verificar se está funcionando
python -m app.main
```

### 3. Configuração Frontend (3 min)
```bash
cd RagBot-Front

# Verificar versão do Node.js
node --version  # Deve ser 20.19+ ou 22.12+

# Instalar dependências
npm install

# Configurar variáveis de ambiente
copy .env.example .env
# Ajustar .env se necessário

# Iniciar desenvolvimento
npm run dev
```

### 4. Acesso ao Sistema
- **Frontend**: http://localhost:5173
- **Backend API**: http://localhost:8000
- **API Docs**: http://localhost:8000/docs
- **Health Check**: http://localhost:8000/health

---

## 🐍 Configuração Detalhada do Backend

### Pré-requisitos Python

!!! danger "Versão Python Crítica"
    **Use APENAS Python 3.11.x**. Versões 3.12+ causam incompatibilidades com NumPy/LangChain.
    
    Download oficial: [Python 3.11.8](https://www.python.org/downloads/release/python-3118/)

**Verificação de Versão:**
```bash
# Deve mostrar Python 3.11.x
python --version

# Se necessário, use python3.11 no Linux/Mac
python3.11 --version
```

### 1. Ambiente Virtual

=== "Windows (PowerShell)"
    ```powershell
    # Criar ambiente virtual
    python -m venv venv
    
    # Ativar ambiente
    .\venv\Scripts\Activate.ps1
    
    # Se der erro de política de execução
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
    .\venv\Scripts\Activate.ps1
    
    # Verificar se ativou (deve aparecer (venv) no prompt)
    python --version
    ```

=== "Windows (CMD)"
    ```cmd
    # Criar ambiente virtual
    python -m venv venv
    
    # Ativar ambiente
    venv\Scripts\activate.bat
    
    # Verificar se ativou
    python --version
    ```

=== "Linux/macOS"
    ```bash
    # Criar ambiente virtual
    python3.11 -m venv venv
    
    # Ativar ambiente
    source venv/bin/activate
    
    # Verificar se ativou
    python --version
    ```

### 2. Instalação de Dependências

```bash
# Atualizar pip para última versão
python -m pip install --upgrade pip

# Verificar pip (deve ser 23.0+)
pip --version

# Instalar todas as dependências
pip install -r requirements.txt

# Verificar instalação (deve listar ~35 pacotes)
pip list | wc -l
```

**Possíveis Problemas:**

=== "Erro NumPy Python 3.12+"
    ```bash
    # Erro: Failed building wheel for numpy
    # Solução: Usar Python 3.11
    ```

=== "Erro psycopg2"
    ```bash
    # Linux: instalar dependências de sistema
    sudo apt-get install python3-dev libpq-dev
    
    # macOS: instalar postgres
    brew install postgresql
    ```

=== "Erro sentence-transformers"
    ```bash
    # Instalar PyTorch primeiro
    pip install torch --index-url https://download.pytorch.org/whl/cpu
    pip install sentence-transformers
    ```

### 3. Configuração de Variáveis de Ambiente

```bash
# Copiar arquivo exemplo
copy .env.example .env  # Windows
cp .env.example .env    # Linux/Mac
```

**Editar arquivo `.env`:**
```bash
# ========================================
# RAGBot Backend - Environment Variables
# ========================================

# Application
DEBUG=true
LOG_LEVEL=INFO
HOST=0.0.0.0
PORT=8000

# Database
DATABASE_URL=postgresql://tccrag:tcc123@localhost:5432/ragbot_db
DATABASE_POOL_SIZE=20
DATABASE_MAX_OVERFLOW=0

# Google Gemini AI (OBRIGATÓRIO)
GEMINI_API_KEY=sua-api-key-aqui

# CORS (ajustar para produção)
ALLOWED_ORIGINS=["http://localhost:5173","http://localhost:3000"]

# File Upload
MAX_FILE_SIZE_MB=50
UPLOAD_FOLDER=./documents

# RAG Configuration
CHUNK_SIZE=1000
CHUNK_OVERLAP=200
MAX_CHUNKS_PER_QUERY=10
SIMILARITY_THRESHOLD=0.7

# Embedding Model
EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2
EMBEDDING_DIMENSION=384
```

**Como obter Gemini API Key:**
1. Acesse [Google AI Studio](https://aistudio.google.com/app/apikey)
2. Clique em "Create API Key"
3. Copie a key e cole no arquivo `.env`

### 4. Configuração do Banco de Dados

```bash
# Subir PostgreSQL com pgvector
docker-compose up -d

# Verificar se está rodando
docker ps

# Verificar logs (se necessário)
docker-compose logs postgres

# Testar conexão
python -c "
from db.manager import db_manager
print('DB Status:', db_manager.test_connection())
"
```

**Schema do Banco:**
O banco é inicializado automaticamente com:
- ✅ Extensão `pgvector`
- ✅ Tabelas: `documents`, `chunks`, `embeddings`
- ✅ Índices otimizados
- ✅ Triggers e constraints

### 5. Teste do Backend

```bash
# Iniciar aplicação
python -m app.main

# Em outro terminal, testar endpoints
curl http://localhost:8000/health
curl http://localhost:8000/

# Acessar documentação
# Navegador: http://localhost:8000/docs
```

### 6. Ingestão de Documentos (Opcional)

```bash
# Colocar PDFs na pasta documents/
# Executar script de ingestão
python scripts/ingest.py

# Ou testar upload via API
curl -X POST "http://localhost:8000/api/documents/upload" \
  -F "file=@documento.pdf"
```

---

## 🎨 Configuração Detalhada do Frontend

### Pré-requisitos Node.js

**Versões Suportadas:**
- ✅ **Node.js 20.19.0+** (LTS recomendado)
- ✅ **Node.js 22.12.0+** (Current)

**Verificação e Instalação:**
```bash
# Verificar versão atual
node --version
npm --version

# Se não tiver Node.js instalado:
# Windows: https://nodejs.org/
# Linux: curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
# macOS: brew install node@20
```

### 1. Instalação de Dependências

```bash
cd RagBot-Front

# Instalar todas as dependências
npm install

# Verificar se instalou corretamente (deve listar ~45 pacotes)
npm list --depth=0

# Limpar cache se der problemas
npm cache clean --force
npm install
```

**Dependências Principais:**
```json
{
  "vue": "^3.5.22",
  "typescript": "~5.9.0", 
  "vite": "^7.1.7",
  "tailwindcss": "^3.4.18",
  "pinia": "^3.0.3",
  "vue-router": "^4.5.1",
  "marked": "^16.3.0"
}
```

### 2. Configuração de Variáveis

```bash
# Copiar arquivo exemplo
copy .env.example .env  # Windows
cp .env.example .env    # Linux/Mac
```

**Arquivo `.env` do Frontend:**
```bash
# ========================================
# RAGBot Frontend - Environment Variables  
# ========================================

# API Backend URL (deixe vazio para usar mocks em desenvolvimento)
VITE_API_BASE_URL=http://localhost:8000

# Links externos
VITE_GITHUB_URL=https://github.com/TCC-RagBot

# Features (para desenvolvimento)
VITE_ENABLE_MOCK=false
VITE_ENABLE_DEBUG=true

# Theme
VITE_DEFAULT_THEME=light
```

### 3. Desenvolvimento

```bash
# Iniciar servidor de desenvolvimento
npm run dev

# Acessar aplicação
# Navegador: http://localhost:5173

# Outros comandos úteis
npm run build        # Build para produção
npm run preview      # Preview do build
npm run type-check   # Verificação de tipos
npm run lint         # Linting
npm run test:unit    # Testes unitários
```

### 4. Configuração do VS Code

**Extensões Recomendadas:**
```json
{
  "recommendations": [
    "vue.volar",
    "vue.typescript-vue-plugin",
    "bradlc.vscode-tailwindcss",
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint"
  ]
}
```

**Settings do Workspace:**
```json
{
  "typescript.preferences.includePackageJsonAutoImports": "auto",
  "vue.codeActions.enabled": true,
  "tailwindCSS.includeLanguages": {
    "vue": "html"
  },
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

---

## 🐳 Docker Development (Opcional)

### Backend Docker
```dockerfile
# Dockerfile para desenvolvimento
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python", "-m", "app.main"]
```

### Frontend Docker  
```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json .
RUN npm ci

COPY . .

CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]
```

### Docker Compose Completo
```yaml
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

  backend:
    build: ./RagBot-Back
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://tccrag:tcc123@postgres:5432/ragbot_db
    depends_on:
      - postgres
    volumes:
      - ./RagBot-Back:/app

  frontend:
    build: ./RagBot-Front
    ports:
      - "5173:5173"
    environment:
      - VITE_API_BASE_URL=http://localhost:8000
    volumes:
      - ./RagBot-Front:/app

volumes:
  postgres_data:
```

---

## 🧪 Configuração de Testes

### Testes Backend (pytest)

```bash
cd RagBot-Back

# Instalar dependências de teste (já incluídas)
pip install pytest pytest-asyncio

# Executar todos os testes
python -m pytest tests/ -v

# Executar teste específico
python -m pytest tests/test_ingestion.py -v -s

# Com cobertura
pip install pytest-cov
python -m pytest tests/ --cov=app --cov-report=html
```

### Testes Frontend (Vitest)

```bash
cd RagBot-Front

# Executar testes unitários
npm run test:unit

# Executar em modo watch
npm run test:unit -- --watch

# Com cobertura
npm run test:unit -- --coverage
```

---

## 🔧 Troubleshooting

### Problemas Comuns Backend

=== "Erro de Conexão DB"
    ```bash
    # Verificar se Docker está rodando
    docker ps
    
    # Reiniciar containers
    docker-compose down
    docker-compose up -d
    
    # Verificar logs
    docker-compose logs postgres
    ```

=== "Erro Python 3.12+"
    ```bash
    # Desinstalar ambiente atual
    rm -rf venv/
    
    # Reinstalar com Python 3.11
    python3.11 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    ```

=== "Erro Gemini API"
    ```bash
    # Verificar se API key está configurada
    python -c "
    from app.config.settings import settings
    print('Gemini Key:', settings.gemini_api_key[:10] + '...')
    "
    ```

### Problemas Comuns Frontend

=== "Erro Node.js Versão"
    ```bash
    # Instalar versão correta
    nvm install 20.19.0  # se usar nvm
    nvm use 20.19.0
    
    # Ou baixar diretamente do site
    # https://nodejs.org/
    ```

=== "Erro npm install"
    ```bash
    # Limpar cache e reinstalar
    rm -rf node_modules package-lock.json
    npm cache clean --force
    npm install
    ```

=== "Erro CORS"
    ```bash
    # Verificar se backend está rodando
    curl http://localhost:8000/health
    
    # Verificar configuração CORS no .env backend
    # ALLOWED_ORIGINS=["http://localhost:5173"]
    ```

### Comandos de Debug

```bash
# Backend - verificar status geral
python -c "
from app.config.settings import settings
from db.manager import db_manager
print('Settings OK:', bool(settings.gemini_api_key))
print('DB OK:', db_manager.test_connection())
"

# Frontend - verificar build
npm run build
npm run preview

# Verificar logs em tempo real
# Backend: tail -f logs/ragbot.log
# Frontend: browser console (F12)
```

---

## 📱 Configuração IDE/Editor

### VS Code Extensions

**Essenciais:**
```json
{
  "recommendations": [
    "ms-python.python",
    "ms-python.pylint", 
    "vue.volar",
    "bradlc.vscode-tailwindcss",
    "ms-vscode.vscode-typescript-next",
    "esbenp.prettier-vscode"
  ]
}
```

### Settings Recomendados

```json
{
  "python.defaultInterpreterPath": "./venv/bin/python",
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": true,
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll": true
  }
}
```

---

## 🚀 Próximos Passos

Após completar o setup:

1. 📖 **Leia a [Arquitetura](../engenharia/arquitetura.md)** do sistema
2. 🔧 **Explore a [API Reference](api.md)** para entender endpoints
3. 🎨 **Veja a [documentação do Frontend](frontend.md)** para UI
4. 🐍 **Leia sobre o [Backend](backend.md)** para lógica de negócio
5. 🧪 **Execute os testes** para validar funcionamento

!!! success "Ambiente Configurado com Sucesso!"
    Se chegou até aqui, seu ambiente está pronto para desenvolvimento. O RAGBot deve estar funcionando em modo completo com frontend e backend integrados.

**Última atualização:** 19 de novembro de 2025

Orientações para configurar o ambiente de desenvolvimento local, instalar dependências e executar o projeto.
