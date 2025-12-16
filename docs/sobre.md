# Objetivos e Justificativa

## Objetivo Geral

Desenvolver um sistema de chat inteligente baseado em RAG (Retrieval-Augmented Generation) que permita à comunidade acadêmica da UnB consultar informações contidas em documentos institucionais de forma rápida, precisa e eficiente, eliminando a necessidade de busca manual em múltiplos arquivos PDF.

## Objetivos Específicos

### 1. Interface do Usuário
Criar uma interface web intuitiva e acessível que:

- Proporcione uma experiência de uso simples e agradável
- Funcione de forma responsiva em diferentes dispositivos
- Ofereça suporte a temas claro e escuro
- Apresente respostas de forma clara com citação de fontes

### 2. Backend e Pipeline RAG
Desenvolver uma API robusta capaz de:

- Processar e ingerir documentos PDF automaticamente
- Realizar busca semântica eficiente em grandes volumes de texto
- Interpretar perguntas em linguagem natural
- Gerar respostas baseadas exclusivamente no conteúdo dos documentos

### 3. Armazenamento e Recuperação
Implementar uma solução de banco de dados que:

- Armazene embeddings vetoriais para busca semântica
- Mantenha registro de conversas e interações
- Permita escalabilidade conforme o volume de documentos cresce
- Garanta performance adequada nas consultas

### 4. Qualidade e Confiabilidade
Assegurar a qualidade do sistema através de:

- Testes automatizados das funcionalidades principais
- Containerização para facilitar deploy e manutenção
- Documentação técnica clara e completa
- Respostas verificáveis com citação de fontes

## Justificativa

### Ganho de Tempo
A busca manual em atas pode levar horas quando se precisa verificar múltiplas reuniões. O RAGBot reduz esse tempo para segundos, permitindo que a comunidade acadêmica foque em atividades mais estratégicas.

### Acessibilidade da Informação
Muitas informações importantes ficam "perdidas" em documentos que poucas pessoas conseguem localizar. O sistema democratiza o acesso a essas informações, tornando-as disponíveis para toda a comunidade.

### Precisão e Confiabilidade
Diferente de uma busca genérica ou respostas de IA não fundamentadas, o RAGBot sempre cita as fontes e baseia suas respostas exclusivamente nos documentos fornecidos, aumentando a confiança nas informações.

### Aplicabilidade Ampla
Embora desenvolvido para atas da UnB, a solução pode ser adaptada para outros contextos que envolvam consulta a grandes volumes de documentos:

- Documentação técnica de empresas
- Arquivos jurídicos e processos
- Bases de conhecimento corporativas
- Material didático e acadêmico

### Relevância Tecnológica
O projeto utiliza tecnologias modernas e em alta demanda no mercado:

- **RAG**: Técnica consolidada para sistemas de IA confiáveis
- **Embeddings vetoriais**: Fundamental para busca semântica
- **API REST moderna**: Padrão da indústria para serviços web
- **Containerização**: Essencial para DevOps e Cloud

A implementação bem-sucedida deste TCC demonstra competências técnicas relevantes para o mercado de trabalho em Engenharia de Software.
