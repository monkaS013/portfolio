# Vinicius Morais — AI & Automation Engineer

*[Português](README.pt-BR.md)*

I build AI and automation systems that reach production. This portfolio holds two kinds of entries: open-source repositories you can read and run, and case studies of internal systems I built at work — written without any proprietary code, data, or secrets.

Short version of who I am: an electrical engineer who moved into data and AI. Over the past year I shipped a RAG assistant that answers HR questions in production, an internal HR platform a whole company uses every day, data pipelines over millions of rows, and a few smaller tools I keep for myself.

## Open-source code

| Repo | What it does | Stack |
|---|---|---|
| [claude-usage-monitor](https://github.com/monkaS013/claude-usage-monitor) | Floating Windows widget with the real usage numbers of the Claude plan, with no network calls | Python, Tkinter |
| [nl2sql-ecommerce-agent](https://github.com/monkaS013/nl2sql-ecommerce-agent) | Natural-language-to-SQL agent over a public e-commerce dataset, with a SQL safety layer and a self-repair loop | Python, DuckDB, Streamlit |
| [doc-analyzer-evals](https://github.com/monkaS013/doc-analyzer-evals) | Structured document extraction with a gold-standard eval (precision/recall/F1) | Python, Claude, Pydantic |

## Case studies (production systems, sanitized)

These describe systems I built inside a company. No proprietary code, real data, or company identity — just the problem, the architecture, and the engineering.

| Case | Problem | Highlights |
|---|---|---|
| [Ask-HR — RAG in production](case-studies/ask-hr-rag.md) | Employees couldn't find answers buried in HR policies | pgvector + LangGraph · gold-standard eval as a release gate · OWASP-LLM guardrails · source citation |
| [Internal HRIS](case-studies/hris.md) | HR ran on scattered spreadsheets and email | Next.js/TypeScript · role-based access · ~557 tests · deploy on push |
| [Market intelligence](case-studies/market-intelligence.md) | No visibility into sell-in per competitor | DuckDB pipeline over 4.5M rows · open regulatory data |
| [Multi-agent market study](case-studies/multi-agent-market-study.md) | Deciding whether to import a new product line | orchestrator + 6 parallel research agents |
| [Executive dashboards](case-studies/executive-dashboards.md) | Leadership had no single view of the operation | auto-refreshing dashboards · multi-entity, multi-currency |

## AI tooling / agent skills

Skills I built or extended for my own agent workflow — see [ai-skills.md](ai-skills.md). It's explicit about what's original and what extends another open-source project (credited).

## Background

- B.Sc. Electrical Engineering, Universidade Santa Cecília
- Data & AI at an energy-sector importer; before that, demand planning and S&OP
- Python · RAG · LangGraph · agents · FastAPI · Next.js · DuckDB · SQL · Power BI

## Contact

- LinkedIn: [linkedin.com/in/vinicius-morais-ai](https://www.linkedin.com/in/vinicius-morais-ai)
- Email: vinicius_morais99@hotmail.com
