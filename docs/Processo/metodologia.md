# Metodologia de Desenvolvimento

## Metodologia Ágil - Scrum

O projeto foi desenvolvido utilizando **Scrum**, um framework ágil que organiza o trabalho em ciclos curtos e iterativos, promovendo entregas incrementais e adaptação contínua.

### Estrutura das Sprints

**Duração:** 15 dias (2 semanas)

**Atividades por Sprint:**

- **Planning:** Definição das tarefas e criação de issues no GitHub
- **Desenvolvimento:** Implementação das funcionalidades planejadas
- **Review:** Validação das entregas e testes
- **Retrospectiva:** Discussão de melhorias para a próxima sprint

### Equipe

O time era composto por **2 desenvolvedores** que compartilhavam todas as responsabilidades:

- Planejamento das sprints
- Divisão de tarefas
- Desenvolvimento do código
- Revisão de código (code review)
- Testes e validação

## GitHub Projects - Kanban

Para organizar o trabalho, utilizamos o **GitHub Projects** com quadro Kanban digital.

### Estrutura do Quadro

O Kanban foi dividido em 4 colunas principais:

- **Backlog:** Issues planejadas mas não iniciadas
- **To Do:** Tarefas da sprint atual
- **In Progress:** Issues em desenvolvimento
- **Done:** Tarefas concluídas e revisadas

### Fluxo de Trabalho

1. **Planning:** Issues criadas e movidas para "To Do"
2. **Desenvolvimento:** Developer move issue para "In Progress"
3. **Code Review:** PR aberto, parceiro revisa
4. **Conclusão:** Após merge, issue vai para "Done"

### Vantagens do Kanban

- **Visibilidade:** Todos sabem o status de cada tarefa
- **Organização:** Priorização clara do que fazer
- **Histórico:** Rastreamento de tudo que foi desenvolvido
- **Métricas:** Acompanhamento de velocity e produtividade

## Cerimônias do Scrum

### Sprint Planning

**Quando:** Início de cada sprint (a cada 15 dias)

**Atividades:**

- Revisão do backlog
- Seleção de funcionalidades para a sprint
- Criação de issues no GitHub
- Estimativa de esforço
- Definição de critérios de aceite

### Daily (Assíncrono)

Como a equipe era pequena e trabalhava de forma flexível, não realizávamos dailies formais. A comunicação acontecia via:

- Comentários nas issues
- Mensagens no WhatsApp/Discord
- Commits descritivos no Git

### Sprint Review

**Quando:** Final de cada sprint

**Atividades:**

- Demonstração das funcionalidades desenvolvidas
- Validação dos critérios de aceite
- Testes das features implementadas
- Feedback e ajustes necessários

### Retrospectiva

**Quando:** Final de cada sprint

**Atividades:**

- Discussão do que funcionou bem
- Identificação de problemas encontrados
- Definição de melhorias para a próxima sprint
- Ajustes no processo de trabalho

## Organização por Issues

Cada funcionalidade ou melhoria era representada por uma **issue** no GitHub.

### Estrutura de uma Issue

**Título:** Descrição clara e objetiva

**Descrição:**

- Contexto da necessidade
- Funcionalidade esperada
- Critérios de aceite
- Referências (se aplicável)

**Labels:**

- `feature`: Nova funcionalidade
- `bug`: Correção de erro
- `enhancement`: Melhoria
- `documentation`: Documentação

**Assignee:** Developer responsável

**Projeto:** Vinculada ao board do Kanban

### Exemplos de Issues

- "Implementar busca semântica com pgvector"
- "Criar interface de chat responsiva"
- "Adicionar endpoint de upload de documentos"
- "Configurar CI/CD com GitHub Actions"

## Entregas Incrementais

O desenvolvimento seguiu uma abordagem incremental:

### Sprint 1-2: Fundação

- Configuração do ambiente de desenvolvimento
- Estrutura básica do backend (FastAPI)
- Configuração do banco de dados (PostgreSQL + pgvector)
- Prova de conceito do RAG

### Sprint 3-4: Core do Sistema

- Pipeline de ingestão de documentos
- Busca vetorial com embeddings
- Integração com Gemini AI
- API REST funcional

### Sprint 5-6: Interface e Integração

- Interface web com Vue 3
- Chat funcional com histórico
- Upload de documentos via frontend
- Integração frontend-backend completa

### Sprint 7-8: Qualidade e Deploy

- Testes automatizados
- Containerização com Docker
- Documentação técnica
- Refinamentos e correções

## Ferramentas Utilizadas

### Gestão de Projeto

- **GitHub Projects:** Kanban e tracking
- **GitHub Issues:** Gerenciamento de tarefas
- **GitHub Milestones:** Organização por sprints

### Comunicação

- **WhatsApp/Discord:** Comunicação rápida
- **GitHub Discussions:** Decisões técnicas
- **Google Meet:** Reuniões síncronas

### Desenvolvimento

- **VS Code:** IDE principal
- **Git:** Controle de versão
- **Docker:** Ambiente isolado
- **Postman:** Testes de API

## Adaptações do Scrum

Por ser uma equipe pequena e acadêmica, algumas adaptações foram feitas:

**Flexibilidade:**

- Sem dailies formais diárias
- Comunicação assíncrona quando possível
- Reuniões síncronas para decisões importantes

**Pragmatismo:**

- Foco em entregas funcionais
- Documentação contínua mas enxuta
- Testes manuais complementando automatizados

**Colaboração:**

- Pair programming para funcionalidades complexas
- Code review obrigatório em todos os PRs
- Compartilhamento de conhecimento constante

Essa abordagem permitiu manter a agilidade do Scrum adaptada à realidade do projeto acadêmico, garantindo entregas consistentes e qualidade do código.
