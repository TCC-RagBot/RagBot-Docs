# Git Flow e Colaboração

## Estratégia de Versionamento

O projeto utilizou **Git Flow** adaptado para uma equipe de 2 desenvolvedores, combinando trabalho síncrono e assíncrono de forma eficiente.

## Estrutura de Branches

### Branch Principal

**`main`**

- Código de produção estável
- Apenas aceita merges via Pull Request
- Protegida contra commits diretos

### Branches de Feature

**Padrão:** `feature/<nome-da-funcionalidade>`

**Exemplos:**

- `feature/vector-search`
- `feature/chat-interface`
- `feature/document-upload`

Cada issue gerava uma branch isolada, garantindo desenvolvimento independente e seguro.

## Fluxo de Trabalho

### 1. Sprint Planning

Na reunião de planning, as tarefas eram organizadas:

- Discussão das funcionalidades da sprint
- Criação de issues no GitHub
- Definição de prioridades
- Atribuição de responsabilidades

### 2. Desenvolvimento Isolado

Cada desenvolvedor:

- Escolhe uma issue do backlog
- Cria branch específica: `git checkout -b feature/nome-issue`
- Desenvolve a funcionalidade localmente
- Faz commits frequentes e descritivos

**Exemplo de commits:**

```bash
git commit -m "feat: adiciona busca vetorial com pgvector"
git commit -m "fix: corrige encoding de PDFs com acentuação"
git commit -m "docs: atualiza README com instruções de setup"
```

### 3. Pull Request

Ao finalizar a funcionalidade:

**Abertura do PR:**

- Desenvolver push da branch para o repositório
- Abre Pull Request no GitHub
- Descreve as mudanças realizadas
- Referencia a issue relacionada

**Exemplo de descrição:**

```markdown
## Descrição
Implementa busca semântica usando pgvector e embeddings.

## Mudanças
- Adiciona repositório de busca vetorial
- Integra sentence-transformers para embeddings
- Cria índice HNSW no PostgreSQL

## Testes
- [x] Busca retorna chunks relevantes
- [x] Performance < 200ms para 1000 documentos

Closes #15
```

### 4. Code Review

O outro desenvolvedor revisa o código:

**Checklist de Revisão:**

- Código está legível e bem organizado?
- Funcionalidade atende aos critérios de aceite?
- Testes foram adicionados/atualizados?
- Documentação foi atualizada?
- Não há bugs evidentes?

**Ações possíveis:**

- **Aprovar:** Se tudo estiver ok
- **Solicitar mudanças:** Se houver problemas
- **Comentar:** Para dúvidas ou sugestões

### 5. Merge

Após aprovação:

- Resolve conflitos (se houver)
- Merge da branch para `main`
- Delete da branch de feature
- Fecha a issue automaticamente

## Trabalho Síncrono vs Assíncrono

### Desenvolvimento Síncrono

**Quando:** Funcionalidades core ou decisões arquiteturais

**Como:** Reuniões de pair programming

**Exemplos:**

- Definição da arquitetura do sistema
- Implementação do pipeline RAG
- Decisões sobre modelo de embeddings
- Estruturação do banco de dados

**Vantagens:**

- Decisões rápidas e consensuais
- Compartilhamento de conhecimento imediato
- Menos erros de comunicação
- Maior alinhamento técnico

### Desenvolvimento Assíncrono

**Quando:** Features isoladas e independentes

**Como:** Cada um trabalha em sua issue

**Exemplos:**

- Estilização de componentes frontend
- Criação de endpoints REST
- Escrita de testes unitários
- Documentação técnica

**Vantagens:**

- Flexibilidade de horários
- Desenvolvimento paralelo de múltiplas features
- Foco individual em cada tarefa
- Progresso contínuo

## Organização por Issues

### Anatomia de uma Issue

**Título:** Claro e objetivo

```
Implementar busca semântica com pgvector
```

**Descrição:**

```markdown
## Contexto
O sistema precisa buscar documentos relevantes baseado na pergunta do usuário.

## Tarefa
Implementar busca vetorial usando pgvector e sentence-transformers.

## Critérios de Aceite
- [ ] Embeddings são gerados para documentos
- [ ] Busca retorna top-k chunks mais similares
- [ ] Performance < 200ms para 1000 chunks

## Referências
- Documentação pgvector: [link]
- Paper sobre embeddings: [link]
```

**Labels:**

- `feature`: Nova funcionalidade
- `bug`: Correção de erro
- `enhancement`: Melhoria de algo existente
- `documentation`: Atualização de docs

**Milestone:** Sprint atual

**Assignee:** Desenvolvedor responsável

### Vinculação ao Kanban

Cada issue é automaticamente refletida no GitHub Projects:

- Criada → vai para **Backlog**
- Assignee definido → vai para **To Do**
- Branch criada → vai para **In Progress**
- PR merged → vai para **Done**

## Convenções de Commit

O projeto seguiu **Conventional Commits** para mensagens padronizadas:

**Formato:**

```
<tipo>(<escopo>): <descrição>

[corpo opcional]

[footer opcional]
```

**Tipos:**

- `feat`: Nova funcionalidade
- `fix`: Correção de bug
- `docs`: Documentação
- `style`: Formatação, espaçamento
- `refactor`: Refatoração de código
- `test`: Adição de testes
- `chore`: Tarefas de build, configuração

**Exemplos:**

```bash
feat(api): adiciona endpoint de upload de documentos

fix(chat): corrige exibição de mensagens longas

docs(readme): atualiza instruções de instalação

refactor(services): extrai lógica de prompt para função separada
```

## Revisão de Código

### Benefícios do Code Review

**Qualidade:**

- Detecção precoce de bugs
- Sugestões de melhorias
- Manutenção de padrões de código

**Conhecimento:**

- Ambos conhecem todo o código
- Transferência de conhecimento contínua
- Menos risco de "código proprietário"

**Colaboração:**

- Discussões técnicas construtivas
- Alinhamento de abordagens
- Senso de propriedade coletiva

### Processo de Revisão

1. Revisor lê a descrição do PR
2. Entende o contexto da mudança
3. Analisa o código linha por linha
4. Testa localmente (se necessário)
5. Deixa comentários construtivos
6. Aprova ou solicita mudanças

## Resolução de Conflitos

**Estratégia:** Resolver conflitos sempre que possível localmente antes do merge.

**Processo:**

```bash
# Atualizar branch com main
git checkout feature/minha-feature
git pull origin main

# Resolver conflitos manualmente
# Testar que tudo funciona

git add .
git commit -m "merge: resolve conflitos com main"
git push
```

## Proteção da Branch Main

**Regras configuradas:**

- ✅ Pull Request obrigatório para merge
- ✅ Pelo menos 1 aprovação necessária
- ✅ CI/CD deve passar antes do merge
- ✅ Branch deve estar atualizada com main

Essas proteções garantem que código não testado ou não revisado nunca chegue à produção.

## Ferramentas Utilizadas

**GitHub:**

- Repositórios Git
- Issues e Projects
- Pull Requests
- Actions (CI/CD)

**VS Code:**

- GitLens: Visualização de histórico
- Git Graph: Visualização de branches
- GitHub Pull Requests: Revisão no editor

## Boas Práticas Implementadas

**Commits Pequenos:**

Commits frequentes e focados facilitam revisão e debug.

**Branches Curtas:**

Features eram desenvolvidas em 1-3 dias, evitando branches longas e conflitos.

**Descrições Claras:**

PRs sempre tinham contexto, mudanças e testes descritos.

**Testes Antes do Merge:**

Funcionalidade era testada localmente antes de abrir PR.

**Delete de Branches:**

Branches mergeadas eram deletadas para manter repositório limpo.

## Exemplo de Fluxo Completo

### Cenário: Implementar Upload de Documentos

**1. Planning:**

- Issue #23 criada: "Implementar upload de PDFs"
- Atribuída ao Dev A

**2. Desenvolvimento:**

```bash
git checkout -b feature/document-upload
# ... desenvolvimento ...
git add .
git commit -m "feat(api): adiciona endpoint de upload"
git push origin feature/document-upload
```

**3. Pull Request:**

- Dev A abre PR #24
- Descreve as mudanças
- Referencia issue #23

**4. Code Review:**

- Dev B revisa código
- Testa localmente
- Aprova PR

**5. Merge:**

- Dev A faz merge para main
- Issue #23 fechada automaticamente
- Branch deletada

**6. Deploy:**

- CI/CD roda testes
- Deploy automático se tudo passar

Esse fluxo garantiu que todas as funcionalidades passassem por dupla validação, mantendo alta qualidade do código e conhecimento compartilhado.
