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

**Reprodutibilidade:**

- Ambiente idêntico para todos
- Versões fixas de PostgreSQL, Python, etc

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

## Deployment Local

O projeto foi desenvolvido para execução **local**, sem deploy em produção:

**Justificativa:**

- Foco no desenvolvimento e aprendizado
- Projeto acadêmico/TCC
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

**Segurança:**

- Secrets em `.env` (não commitados)
- `.gitignore` configurado corretamente
- Portas não expostas desnecessariamente

## Evolução Futura

Possíveis melhorias para produção:

### Deploy na Cloud

**Opções:**

- AWS ECS
- Azure Container Apps
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

Apesar de não ter sido implantado em produção, o uso de Docker demonstrou boas práticas de DevOps e preparou o sistema para um eventual deploy futuro com mínimas modificações.
