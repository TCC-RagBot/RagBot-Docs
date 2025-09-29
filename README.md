# RagBot - Documentação do Projeto TCC

![MkDocs Material](https://img.shields.io/badge/MkDocs-Material-blue?logo=materialformkdocs)
![Python](https://img.shields.io/badge/Python-3.8%2B-blue?logo=python)
![Status](https://img.shields.io/badge/Status-Em%20Desenvolvimento-yellow)

## Sobre o Projeto

Este repositório contém a documentação técnica completa do projeto RagBot, desenvolvido como Trabalho de Conclusão de Curso (TCC) em Engenharia de Software.

## 🤖 O que é o RagBot?

O RagBot é um sistema de chatbot inteligente que utiliza a técnica RAG (Retrieval-Augmented Generation) para fornecer respostas contextualmente relevantes baseadas em documentos e conhecimento específico.

## 📋 Estrutura da Documentação

Esta documentação está organizada nas seguintes seções:

- **Sobre o Projeto**: Visão geral, objetivos e escopo
- **Engenharia de Software**: Metodologias, processos e práticas aplicadas
- **Desenvolvimento**: Tecnologias, arquitetura e guias de implementação
- **Testes**: Estratégias e cobertura de testes
- **Deploy**: Guias de implantação e configuração
- **Gestão do Projeto**: Sprints, riscos, custos e qualidade

## 🚀 Acessar Documentação

A documentação completa está disponível em: [https://TCC-RagBot.github.io/RagBot-Docs](https://TCC-RagBot.github.io/RagBot-Docs)

## 🔧 Desenvolvimento Local

### Pré-requisitos

- Python 3.8+
- pip (gerenciador de pacotes Python)

### Instalação

```bash
# Clone o repositório
git clone https://github.com/TCC-RagBot/RagBot-Docs.git
cd RagBot-Docs

# Instale as dependências
pip install -r requirements.txt

# Execute localmente (será aberto em http://localhost:8000)
mkdocs serve
```

A documentação estará disponível em `http://localhost:8000`

### Comandos Úteis

```bash
# Servir localmente com live reload
mkdocs serve

# Build da documentação
mkdocs build

# Deploy para GitHub Pages
mkdocs gh-deploy
```

## 📁 Estrutura do Projeto

```
RagBot-Docs/
├── docs/                    # Conteúdo da documentação
│   ├── index.md            # Página inicial
│   ├── sobre/              # Seção "Sobre o Projeto"
│   ├── engenharia/         # Seção "Engenharia de Software"
│   ├── desenvolvimento/    # Seção "Desenvolvimento"
│   ├── testes/             # Seção "Testes"
│   ├── deploy/             # Seção "Deploy"
│   ├── gestao/             # Seção "Gestão do Projeto"
│   └── assets/             # Imagens e recursos
├── mkdocs.yml              # Configuração do MkDocs
├── requirements.txt        # Dependências Python
└── README.md               # Este arquivo
```

## 📝 Contribuindo

Este projeto segue um fluxo de trabalho baseado em issues e pull requests para documentar adequadamente o processo de desenvolvimento.

### Fluxo de Contribuição

1. **Criar Issue**: Definir claramente o escopo e objetivos
2. **Criar Branch**: `git checkout -b feature/issue-X-description`
3. **Desenvolver**: Implementar as mudanças necessárias
4. **Commit**: Mensagens descritivas referenciando a issue (#X)
5. **Push**: Enviar para o repositório
6. **Pull Request**: Criar PR linkando à issue
7. **Review**: Auto-review ou review colaborativo
8. **Merge**: Integrar à main branch

### Padrões de Commit

```bash
# Formato recomendado
git commit -m "#<issue_number> <tipo>: <descrição concisa>"

# Exemplos
git commit -m "#1 feat: adicionar seção de metodologia"
git commit -m "#2 docs: atualizar guia de setup local"
git commit -m "#3 fix: corrigir links quebrados na navegação"
```

## 📄 Licença

Este projeto é desenvolvido como parte de um TCC acadêmico.

---

**Desenvolvido por:** [Seu Nome]  
**Curso:** Engenharia de Software  
**Instituição:** [Nome da Instituição]  
**Ano:** 2025