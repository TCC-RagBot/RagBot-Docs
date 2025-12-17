# Requisitos do Sistema

## Persona Principal

### Ana, 22 anos - Estudante de Graduação

**Perfil:**

Ana participa de reuniões do centro acadêmico e frequentemente precisa consultar atas e documentos institucionais. Ela deseja respostas rápidas, sem precisar explorar documentos extensos. Utiliza majoritariamente o celular e valoriza interfaces simples, diretas e intuitivas.

**Necessidades:**

- Obter respostas diretas em linguagem natural
- Visualizar os trechos dos documentos que justificam as respostas
- Utilizar o sistema por meio de dispositivos móveis

**Objetivos:**

- Economizar tempo em buscas manuais
- Confiar nas informações apresentadas
- Acessar o sistema de qualquer lugar

## Requisitos Funcionais

Os requisitos funcionais descrevem os comportamentos esperados do sistema:

**RF01 - Perguntas em Linguagem Natural**

O sistema deve permitir ao usuário inserir perguntas em linguagem natural.

**RF02 - Respostas Baseadas em Documentos**

O sistema deve retornar respostas com base em documentos previamente indexados.

**RF03 - Histórico de Interações**

O sistema deve exibir o histórico de interações no chat.

**RF04 - Recuperação de Trechos**

O sistema deve recuperar os trechos dos documentos que embasam a resposta.

## Requisitos Não Funcionais

Os requisitos não funcionais descrevem atributos de qualidade do sistema:

**RNF01 - Performance**

O sistema deve apresentar respostas em até 5 segundos.

**RNF02 - Responsividade**

A interface deve ser responsiva e acessível em dispositivos móveis.

**RNF03 - Rastreabilidade**

O sistema deve permitir rastreabilidade entre resposta e documento de origem.

**RNF04 - Persistência**

O sistema deve manter persistência de dados utilizando PostgreSQL com pgvector.

## User Stories

As funcionalidades foram especificadas através de User Stories, seguindo o formato: **"Como [persona], quero [funcionalidade], para [benefício]"**.

### Tabela de User Stories

| ID | Persona | Objetivo | Benefício | Critérios de Aceite |
|----|---------|----------|-----------|---------------------|
| **US01** | Lionel | Inserir perguntas em linguagem natural | Obter respostas sem ler documentos extensos | A pergunta é compreendida e respondida com base nos documentos |
| **US02** | Caio | Ver os trechos dos documentos que justificam as respostas | Validar a confiabilidade da informação apresentada | O sistema exibe as passagens documentais usadas na geração da resposta |
| **US03** | Pedro | Revisar histórico de perguntas e respostas | Retomar informações anteriores com facilidade | As interações são armazenadas e podem ser acessadas posteriormente em uma mesma interação |
| **US04** | Alice | Verificar documentos disponíveis | Saber de quais assuntos o sistema possui conhecimento | O frontend deve exibir os documentos cadastrados no banco de dados |
| **US05** | Desenvolvedor | Cadastrar novos documentos no sistema | Manter o sistema atualizado com resoluções recentes | O upload é processado com sucesso e o documento aparece na base de consulta |
| **US06** | Desenvolvedor | Verificar se o documento foi corretamente indexado | Garantir a disponibilidade da informação | O sistema confirma a indexação e permite consulta via busca semântica |
| **US07** | Desenvolvedor | Ter um sistema modular | Facilitar manutenção e evolução futura | Os módulos estão organizados com baixo acoplamento e responsabilidade definida |

## Priorização de Requisitos

Os requisitos foram priorizados seguindo o critério de **valor entregue ao usuário**:

### Alta Prioridade

- RF01, RF02, RF04: Core do sistema RAG
- RNF01, RNF04: Performance e persistência essenciais
- US01, US02: Funcionalidades principais

### Média Prioridade

- RF03: Histórico melhora UX mas não é crítico
- RNF02: Responsividade importante mas desktop funciona
- US03, US04: Features complementares

### Baixa Prioridade

- US05, US06, US07: Funcionalidades para desenvolvedores
- RNF03: Rastreabilidade desejável mas não bloqueante

## Rastreabilidade

### Mapeamento RF → Implementação

**RF01** → Endpoint `/ask` + Frontend chat input

**RF02** → Pipeline RAG (busca vetorial + Gemini)

**RF03** → Tabelas `conversations` e `messages`

**RF04** → Tabela `message_source_chunks` + exibição de sources

### Mapeamento RNF → Solução Técnica

**RNF01** → FastAPI assíncrono + pgvector HNSW + Gemini API

**RNF02** → Tailwind CSS + design mobile-first + Vue 3 reativo

**RNF03** → Relacionamentos no banco + exibição de fontes

**RNF04** → PostgreSQL 15 + extensão pgvector

## Validação dos Requisitos

### Critérios de Aceite

Cada requisito possui critérios claros de validação:

**Exemplo - RF01:**

- ✅ Campo de texto aceita perguntas de até 1000 caracteres
- ✅ Validação no frontend e backend
- ✅ Feedback visual ao enviar pergunta

**Exemplo - RNF01:**

- ✅ Medição de tempo de resposta implementada
- ✅ 95% das consultas respondem em < 5s
- ✅ Timeout configurado em 30s

### Testes de Aceitação

Para cada User Story, foram definidos cenários de teste:

**US01 - Pergunta em Linguagem Natural:**

- Usuário digita "Quais foram as decisões de março?"
- Sistema processa e retorna resposta contextualizada
- Resposta é exibida em até 5 segundos

**US02 - Visualização de Fontes:**

- Sistema retorna resposta
- Seção "Fontes" é exibida abaixo da resposta
- Trechos dos documentos são mostrados com nome do arquivo

## Evolução dos Requisitos

Durante o desenvolvimento, alguns requisitos evoluíram:

### Adições

- **RF05** (implícito): Sistema deve suportar múltiplas conversas simultâneas
- **RNF05** (implícito): Sistema deve ser containerizado para facilitar deploy

### Ajustes

- **RF02**: Inicialmente previa apenas 1 documento, expandido para múltiplos
- **RNF01**: Tempo de resposta ajustado de 3s para 5s após testes

### Descartados

- Upload via drag-and-drop no frontend (adiado para v2.0)
- Autenticação de usuários (sistema decidiu ser anônimo)

Essa flexibilidade é característica do desenvolvimento ágil, onde requisitos podem ser refinados conforme o aprendizado do time e feedback contínuo.
