# Vinicius Morais — AI & Automation Engineer

*[English](README.md)*

Construo sistemas de IA e automação que chegam a produção. Este portfólio tem dois tipos de entrada: repositórios open-source que você pode ler e rodar, e case studies de sistemas internos que construí no trabalho, escritos sem código proprietário, dados reais ou segredos.

Quem eu sou, em resumo: engenheiro eletricista que migrou para dados e IA. No último ano coloquei no ar um assistente RAG que responde dúvidas de RH em produção, um portal de RH que uma empresa inteira usa todo dia, pipelines de dados sobre milhões de linhas e algumas ferramentas menores que uso no meu dia a dia.

## Código open-source

| Repo | O que faz | Stack |
|---|---|---|
| [rag-docs](https://github.com/monkaS013/rag-docs) | RAG sobre um corpus de documentos que roda offline: pipeline em LangGraph (recupera → avalia → roteia → responde ou cai no fallback), cinco guardrails OWASP-LLM e eval de precision@k | Python, LangGraph, pgvector, Claude |
| [brasilapi-mcp](https://github.com/monkaS013/brasilapi-mcp) | Servidor MCP que escrevi do zero, expondo a BrasilAPI pública (CEP, CNPJ, bancos, feriados) como oito ferramentas | Python, MCP SDK v2, httpx |
| [multi-llm-ensemble-extractor](https://github.com/monkaS013/multi-llm-ensemble-extractor) | Roda Claude, Llama e Gemini em paralelo sobre o mesmo documento e consolida campo a campo por voto majoritário, marcando as divergências para revisão | Python, Claude/Gemini/Groq, Pydantic |
| [nl2sql-ecommerce-agent](https://github.com/monkaS013/nl2sql-ecommerce-agent) | Perguntas em linguagem natural sobre um dataset de e-commerce. Ele calcula o número num sandbox e mostra o código e a fonte, em vez de deixar o modelo chutar | Python, DuckDB, Streamlit |
| [doc-analyzer-evals](https://github.com/monkaS013/doc-analyzer-evals) | Extração estruturada de documentos com eval gold-standard (precision/recall/F1) | Python, Claude, Pydantic |
| [claude-usage-monitor](https://github.com/monkaS013/claude-usage-monitor) | Widget flutuante no Windows com os números reais de uso do plano Claude, sem nenhuma chamada de rede | Python, Tkinter |

## Case studies (sistemas em produção, sanitizados)

Descrevem sistemas que construí dentro de uma empresa. Sem código proprietário, dado real ou identificação da empresa: só o problema, a arquitetura e a engenharia.

| Case | Problema | Destaques |
|---|---|---|
| [Pergunte ao RH — RAG em produção](case-studies/ask-hr-rag.md) | O colaborador não achava a resposta escondida nas políticas de RH | pgvector + LangGraph · eval gold-standard como gate de release · guardrails OWASP-LLM · citação da fonte |
| [HRIS interno](case-studies/hris.md) | RH rodava em planilhas soltas e e-mail | Next.js/TypeScript · controle de acesso por papel · ~557 testes · deploy por push |
| [Inteligência de mercado](case-studies/market-intelligence.md) | Falta de visibilidade do sell-in por concorrente | pipeline DuckDB sobre 4,5M de linhas · dados públicos regulatórios |
| [Estudo de mercado multi-agente](case-studies/multi-agent-market-study.md) | Decidir a entrada de uma nova linha de produto | orquestrador + 6 agentes de pesquisa em paralelo |
| [Dashboards executivos](case-studies/executive-dashboards.md) | A liderança não tinha uma visão única da operação | dashboards com atualização automática · multi-empresa, multi-moeda |

## AI tooling / agent skills

Skills que criei ou estendi para o meu próprio fluxo de agentes — veja [ai-skills.md](ai-skills.md). O arquivo deixa claro o que é criação minha e o que estende outro projeto open-source (com crédito).

## Trajetória

- Bacharel em Engenharia Elétrica, Universidade Santa Cecília
- Dados & IA numa importadora do setor de energia; antes, planejamento de demanda e S&OP
- Python · RAG · pgvector · LangGraph · agentes · MCP · evals · FastAPI · Next.js · DuckDB · SQL · Power BI

## Contato

- LinkedIn: [linkedin.com/in/vinicius-morais-ai](https://www.linkedin.com/in/vinicius-morais-ai)
- E-mail: vinicius_morais99@hotmail.com
