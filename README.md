# Vinicius Morais

**AI & Automation Engineer**

*[Português](README.pt-BR.md)*

I build AI and automation systems that reach production. This portfolio has two kinds of entry. There are open-source repositories you can read and run, and there are case studies of internal systems I built at work, written without any proprietary code, data, or secrets.

I'm an electrical engineer who moved into data and AI. Over the past year I shipped a RAG assistant that answers HR questions in production and an internal HR platform that a whole company uses every day. There are also data pipelines over a few million rows, plus some smaller tools I keep for myself.

## Open-source code

| Repo | What it does | Stack |
|---|---|---|
| [rag-docs](https://github.com/monkaS013/rag-docs) | RAG over a document corpus that runs offline: a LangGraph pipeline (retrieve → grade → route → generate or fall back), five OWASP-LLM guardrails, and a precision@k eval | Python, LangGraph, pgvector, Claude |
| [brasilapi-mcp](https://github.com/monkaS013/brasilapi-mcp) | An MCP server I wrote from scratch, exposing the public BrasilAPI (postal codes, company registry, banks, holidays) as eight tools | Python, MCP SDK v2, httpx |
| [multi-llm-ensemble-extractor](https://github.com/monkaS013/multi-llm-ensemble-extractor) | Runs Claude, Llama and Gemini in parallel on the same document and consolidates each field by majority vote, flagging disagreements for review | Python, Claude/Gemini/Groq, Pydantic |
| [nl2sql-ecommerce-agent](https://github.com/monkaS013/nl2sql-ecommerce-agent) | Natural-language questions over an e-commerce dataset. It computes the number in a sandbox and shows the code and the source, instead of letting the model guess it | Python, DuckDB, Streamlit |
| [doc-analyzer-evals](https://github.com/monkaS013/doc-analyzer-evals) | Structured document extraction with a gold-standard eval (precision/recall/F1) | Python, Claude, Pydantic |
| [claude-usage-monitor](https://github.com/monkaS013/claude-usage-monitor) | Floating Windows widget with the real usage numbers of the Claude plan, with no network calls | Python, Tkinter |

## Case studies (production systems, sanitized)

These describe systems I built inside a company. No proprietary code, no real data, no company name. What's left is the problem and how it got solved.

| Case | Problem | Highlights |
|---|---|---|
| [Ask-HR (RAG in production)](case-studies/ask-hr-rag.md) | Employees couldn't find answers buried in HR policies | pgvector + LangGraph · gold-standard eval as a release gate · OWASP-LLM guardrails · source citation |
| [Internal HRIS](case-studies/hris.md) | HR ran on scattered spreadsheets and email | Next.js/TypeScript · role-based access · ~557 tests · deploy on push |
| [Market intelligence](case-studies/market-intelligence.md) | No visibility into sell-in per competitor | DuckDB pipeline over 4.5M rows · open regulatory data |
| [Multi-agent market study](case-studies/multi-agent-market-study.md) | Deciding whether to import a new product line | orchestrator + 6 parallel research agents |
| [Executive dashboards](case-studies/executive-dashboards.md) | Leadership had no single view of the operation | auto-refreshing dashboards · multi-entity, multi-currency |

## AI tooling / agent skills

Skills I built or extended for my own agent workflow are listed in [ai-skills.md](ai-skills.md). The file is explicit about what's original and what extends another open-source project, with credit.

## Background

- B.Sc. Electrical Engineering, Universidade Santa Cecília
- Data & AI at an energy-sector importer; before that, demand planning and S&OP
- Python · RAG · pgvector · LangGraph · agents · MCP · evals · FastAPI · Next.js · DuckDB · SQL · Power BI

## Contact

- LinkedIn: [linkedin.com/in/vinicius-morais-ai](https://www.linkedin.com/in/vinicius-morais-ai)
- Email: vinicius_morais99@hotmail.com
