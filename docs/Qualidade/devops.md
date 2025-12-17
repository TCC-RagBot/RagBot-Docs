# DevOps e Infraestrutura

## Containerização com Docker

O projeto utiliza **Docker** e **Docker Compose** para orquestrar os serviços, facilitando o desenvolvimento e garantindo consistência entre ambientes.

### Por que Docker?

**Isolamento:**

- Cada serviço roda em container próprio
- Dependências isoladas do sistema host
- Evita conflitos de versões

**Portabilidade:**

- Funciona igual em qualquer máquina
- Setup rápido para novos desenvolvedores
- "Funciona na minha máquina" deixa de ser problema

**Reprodutibilidade:**

- Ambiente idêntico para todos
- Versões fixas de PostgreSQL, Python, etc
- Facilita onboarding de novos membros

## Arquitetura de Containers

### Serviços Containerizados

**Backend (FastAPI):**

- Container Python 3.11
- Instala dependências do requirements.txt
- Expõe porta 8000

**Banco de Dados (PostgreSQL + pgvector):**

- Imagem oficial pgvector/pgvector:pg16
- PostgreSQL 16 com extensão pgvector
- Expõe porta 5433
- Volume persistente para dados

**Frontend (Vue 3):**

- Container Node.js com Vite
- Build de produção ou dev server
- Expõe porta 5173

## Docker Compose

O `docker-compose.yml` orquestra os serviços:

```yaml
version: '3.8'

services:
  db:
    image: pgvector/pgvector:pg16
    container_name: ragbot_db
    environment:
      POSTGRES_DB: ragbot_db
      POSTGRES_USER: tccrag
      POSTGRES_PASSWORD: tcc123
    ports:
      - "5433:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql

volumes:
  postgres_data:
```

### Vantagens do Docker Compose

**Simplicidade:**

- Um comando para subir tudo: `docker-compose up`
- Networking automático entre containers
- Gerenciamento de volumes centralizado

**Desenvolvimento:**

- Reiniciar serviços facilmente
- Logs centralizados
- Rebuild rápido após mudanças

## Inicialização Automática

### Script SQL Inicial

O banco de dados é configurado automaticamente na primeira execução:

**`db/init.sql`:**

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "vector";

CREATE TABLE conversations (...);
CREATE TABLE messages (...);
CREATE TABLE documents (...);
```

**Processo:**

1. Container do PostgreSQL inicia
2. Script `init.sql` é executado automaticamente
3. Extensões e tabelas são criadas
4. Banco fica pronto para uso

### Persistência de Dados

**Volume Docker:**

- Dados do PostgreSQL persistem entre restarts
- Localização: Volume `postgres_data`
- Sobrevive a `docker-compose down`
- Apagado apenas com `docker-compose down -v`

## Gestão de Configuração

### Variáveis de Ambiente

As configurações são externalizadas através de arquivos `.env`:

**`.env.example` (template):**

```dotenv
DATABASE_URL=postgresql://tccrag:tcc123@localhost:5433/ragbot_db
GEMINI_API_KEY=
DEBUG=True
LOG_LEVEL=INFO
```

**`.env` (desenvolvimento local):**

```dotenv
DATABASE_URL=postgresql://tccrag:tcc123@db:5432/ragbot_db
GEMINI_API_KEY=sua-chave-aqui
DEBUG=True
LOG_LEVEL=DEBUG
```

### Carregamento de Variáveis

O Pydantic carrega automaticamente do `.env`:

```python
class Settings(BaseSettings):
    database_url: str
    gemini_api_key: str
    debug: bool = False
    log_level: str = "INFO"
    
    class Config:
        env_file = ".env"
```

**Vantagens:**

- Secrets não ficam no código
- Configuração específica por ambiente
- Fácil trocar entre dev/prod
- `.env` no `.gitignore` (segurança)

## Ambientes de Desenvolvimento

### Local Development

**Setup inicial:**

```bash
# 1. Clonar repositório
git clone <repo-url>
cd RagBot-Back

# 2. Configurar variáveis
cp .env.example .env
# Editar .env com suas configurações

# 3. Subir banco de dados
docker-compose up -d

# 4. Instalar dependências Python
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 5. Rodar aplicação
python -m app.main
```

**Fluxo de trabalho:**

```bash
# Ver logs do banco
docker-compose logs -f db

# Reiniciar banco
docker-compose restart db

# Limpar e reiniciar tudo
docker-compose down -v
docker-compose up -d
```

## Scripts Úteis

### Gerenciamento de Containers

**Iniciar serviços:**

```bash
docker-compose up -d        # Background
docker-compose up           # Foreground (ver logs)
```

**Parar serviços:**

```bash
docker-compose stop         # Parar (manter dados)
docker-compose down         # Parar e remover containers
docker-compose down -v      # Parar e apagar volumes
```

**Verificar status:**

```bash
docker-compose ps           # Status dos containers
docker-compose logs db      # Logs do banco
docker-compose logs -f      # Logs em tempo real
```

### Backup e Restore

**Backup do banco:**

```bash
docker exec ragbot_db pg_dump -U tccrag ragbot_db > backup.sql
```

**Restore do banco:**

```bash
docker exec -i ragbot_db psql -U tccrag ragbot_db < backup.sql
```

## Otimizações Implementadas

### Cache de Dependências

**Dockerfile otimizado:**

```dockerfile
# Copiar apenas requirements primeiro (cache)
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copiar código depois
COPY . .
```

**Vantagem:** Se código muda mas dependências não, rebuild é rápido.

### Network Interno

Containers se comunicam via rede interna:

- Backend acessa banco via hostname `db`
- Não precisa expor porta do banco publicamente
- Segurança adicional

### Health Checks

Verificação de saúde dos serviços:

```python
@router.get("/health")
async def health_check():
    return {
        "status": "healthy",
        "database": "connected" if db_manager.test_connection() else "disconnected"
    }
```

## Desafios e Soluções

### Problema: Ordem de Inicialização

**Desafio:** Backend tentava conectar antes do banco estar pronto.

**Solução:**

- Health check na inicialização
- Retry automático de conexão
- Logs claros de erro

### Problema: Permissões no Volume

**Desafio:** PostgreSQL com permissões incorretas no volume.

**Solução:**

- Usar volume Docker nomeado (não bind mount)
- Deixar Docker gerenciar permissões

### Problema: Performance no Windows

**Desafio:** I/O lento em volumes no Windows.

**Solução:**

- Usar WSL2 quando possível
- Volumes Docker nativos mais rápidos

## Ferramentas Auxiliares

### Docker Desktop

Interface gráfica para gerenciar containers:

- Visualizar logs facilmente
- Parar/iniciar containers
- Monitorar uso de recursos
- Acessar terminal dos containers

### VS Code Docker Extension

Extensão para desenvolvimento:

- Explorar containers e imagens
- Attach ao container
- Ver logs inline
- Gerenciar volumes

## Deployment Local

O projeto foi desenvolvido para execução **local**, sem deploy em produção:

**Justificativa:**

- Foco no desenvolvimento e aprendizado
- Projeto acadêmico/TCC
- Dados sensíveis (atas da UnB) devem ficar locais
- Evita custos de infraestrutura cloud

**Execução:**

Qualquer pessoa pode rodar o projeto localmente seguindo o README:

1. Instalar Docker
2. Clonar repositório
3. Configurar `.env`
4. `docker-compose up`
5. Acessar sistema

## Boas Práticas Implementadas

**Documentação:**

- README com instruções claras de setup
- Comentários nos arquivos Docker
- Troubleshooting comum documentado

**Segurança:**

- Secrets em `.env` (não commitados)
- `.gitignore` configurado corretamente
- Portas não expostas desnecessariamente

**Manutenibilidade:**

- Versões fixas de imagens Docker
- Dependências com versões pinadas
- Changelog de mudanças na infra

**Observabilidade:**

- Logs estruturados com Loguru
- Health checks implementados
- Métricas de tempo de resposta

## Evolução Futura

Possíveis melhorias para produção:

### Deploy na Cloud

**Opções:**

- Azure Container Apps
- AWS ECS
- Google Cloud Run
- Railway/Heroku

### CI/CD

**Pipeline completo:**

```yaml
# .github/workflows/deploy.yml
- Rodar testes
- Build da imagem Docker
- Push para registry
- Deploy automático
```

### Orquestração

**Kubernetes:**

- Escalabilidade horizontal
- Load balancing
- Auto-healing

### Monitoramento

**Ferramentas:**

- Prometheus para métricas
- Grafana para dashboards
- Sentry para error tracking

Apesar de não ter sido implantado em produção, o uso de Docker demonstrou boas práticas de DevOps e preparou o sistema para um eventual deploy futuro com mínimas modificações.
