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
## 🔗 Issue Relacionada
Closes #3 

## 📋 Descrição
Foi finalizada a implementação da feature de resposta do chat, utilizando langChain para gerar as queries para o banco de dados, e a API do gemini para gerar a resposta final para o usuário

## 🔧 Tipo de Alteração
- [x] 🆕 **Feature** - Nova funcionalidade
- [ ] 🐛 **Bug Fix** - Correção de bug
- [ ] 📚 **Documentação** - Melhoria na documentação
- [ ] 🧪 **Testes** - Adição ou correção de testes
- [ ] ♻️ **Refatoração** - Melhoria no código sem mudança de funcionalidade
- [ ] 🚀 **Deploy** - Relacionado a deploy e infraestrutura
- [ ] 📦 **Dependências** - Atualização de dependências
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
## 📋 Descrição
Configurar o ambiente de teste e implementar testes no fluxo de ingestão de documentos, fazendo validações

## 🔧 Tipo de Alteração
<!-- Marque o tipo principal desta issue -->
- [ ] 🆕 **Feature** - Nova funcionalidade
- [ ] 🐛 **Bug Fix** - Correção de bug
- [ ] 📚 **Documentação** - Melhoria na documentação
- [x] 🧪 **Testes** - Adição ou correção de testes
- [ ] ♻️ **Refatoração** - Melhoria no código sem mudança de funcionalidade
- [ ] 🚀 **Deploy** - Relacionado a deploy e infraestrutura
- [ ] 🔧 **Manutenção** - Tarefas de manutenção e limpeza
- [ ] 📦 **Dependências** - Atualização de dependências
- [ ] 🎨 **UI/UX** - Melhorias na interface
- [ ] ⚡ **Performance** - Melhorias de performance

##Tarefas
- [ ] Configurar o ambiente de teste
- [ ] Implementar testes unitários para garantir a eficácia do sistema
- [ ] Verificar se o documento PDF é lido pelo python
- [ ] Verificar se o documento é transformado em chunks pela biblioteca LangChain
- [ ] Verificar se são gerados embeddings de 384 dimensões pelo modelo All-mini-LM-l6-v2
- [ ] verificar se é enviado para o banco
```

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