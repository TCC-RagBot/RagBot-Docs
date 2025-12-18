# RAGBot - Sistema de Chat Inteligente

## O Problema

Na Universidade de Brasília, são realizadas dezenas de reuniões administrativas e acadêmicas mensalmente. Cada reunião gera uma ata com decisões importantes, deliberações e encaminhamentos.

**O desafio:** localizar uma decisão específica entre centenas de atas é um processo manual, demorado e ineficiente. É necessário:

- Identificar qual reunião tratou do assunto
- Baixar e abrir o PDF da ata correspondente
- Ler página por página até encontrar a informação
- Repetir o processo se a informação estiver em múltiplas atas

Esse processo consome tempo valioso de estudantes, professores e gestores que precisam consultar decisões anteriores.

## A Solução

O **RAGBot** é um sistema de chat inteligente que utiliza técnicas de Recuperação Aumentada por Geração (RAG) para responder perguntas sobre documentos da UnB em linguagem natural.

### Como funciona

1. **Ingestão de Documentos**: O sistema processa e armazena PDFs das atas em um banco de dados vetorial
2. **Busca Semântica**: Quando o usuário faz uma pergunta, o sistema busca os trechos mais relevantes nos documentos
3. **Geração de Resposta**: Uma IA analisa os trechos encontrados e gera uma resposta natural, sempre baseada exclusivamente no conteúdo dos documentos

### Principais características

- **Respostas precisas**: Baseadas apenas no conteúdo dos documentos carregados
- **Interface intuitiva**: Design limpo estilo ChatGPT, fácil de usar
- **Citação de fontes**: Cada resposta mostra os documentos utilizados
- **Modo anônimo**: Não requer autenticação para uso básico

## Stack Tecnológica

### Frontend
- **Vue 3** + **TypeScript**: Interface moderna e reativa
- **Tailwind CSS**: Design system responsivo
- **Vite**: Build tool de alta performance

### Backend
- **Python 3.11** + **FastAPI**: API REST rápida e robusta
- **LangChain**: Orquestração do pipeline RAG
- **Google Gemini AI**: Modelo de linguagem para geração de respostas

### Dados
- **PostgreSQL 15**: Banco de dados relacional
- **pgvector**: Extensão para busca vetorial
- **sentence-transformers**: Geração de embeddings semânticos

## Repositórios

O projeto está organizado em três repositórios no GitHub:

- [**RagBot-Back**](https://github.com/TCC-RagBot/RagBot-Back) - Backend API
- [**RagBot-Front**](https://github.com/TCC-RagBot/RagBot-Front) - Interface Web
- [**RagBot-Docs**](https://github.com/TCC-RagBot/RagBot-Docs) - Documentação

## Público-Alvo

O sistema foi desenvolvido para atender a comunidade acadêmica da UnB:

- **Estudantes**: Consultar decisões sobre cursos, disciplinas e calendário acadêmico
- **Professores**: Verificar deliberações sobre projetos e questões acadêmicas
- **Gestores**: Acessar rapidamente decisões administrativas anteriores
