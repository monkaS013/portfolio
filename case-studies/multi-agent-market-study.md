# Multi-agent market study

*Português abaixo.*

A research pipeline that decides whether to import and resell a new product line, run by an orchestrator and a fleet of parallel research agents, each one citing its sources.

**Role:** designed and orchestrated the whole pipeline (scope, problem decomposition, agent roles, the deterministic financial layer, and the consolidated report).
**Stack:** Claude (Claude Code) as the multi-agent orchestrator · Python + openpyxl for the deterministic financial model · web research with source capture · a self-contained HTML report.

## The problem

The company had to decide whether to import and resell a line of portable power stations across two Latin American markets. The call depended on six very different fronts (market size and growth, competitors, marketplace pricing, channels and marketing, the regulatory and tax environment, and a competitive benchmark), plus the financials of a pilot container. Researching those fronts by hand is slow and disconnected. The goal was a pipeline that ran the fronts in parallel, with cited sources, and folded everything into one executive report.

## The architecture

An orchestrator with a fan-out/fan-in fleet, and the source of truth split by the nature of the data.

- **Deterministic layer (no LLM).** The financial and import core (costs, prices, break-even) is extracted and computed from a spreadsheet with Python/openpyxl and written to a findings file. The numbers that carry the decision stay out of the model's reach. The LLM reasons over data; it doesn't invent it.
- **Six parallel research agents,** one per external front, each tasked with collecting and citing sources: market size and growth; competitors and positioning; marketplace pricing; target customer, channels, and marketing; the regulatory and tax environment (import duties, mandatory certifications, tax classification); and a consolidated competitive benchmark.
- **Orchestration.** The coordinator locks scope with the stakeholder through structured questions (markets, format, depth), dispatches the agents concurrently, and on fan-in merges each front's findings with the deterministic financial block into a single executive HTML report covering market, competition, SWOT, benchmark, regulatory, a 1–3 year projection, and sources.

## Engineering highlights

- **Deterministic vs researched data, kept separate.** Critical numbers computed in code; market narrative and context researched by the LLM. That split is what keeps hallucination out of the points the decision rests on.
- **Real parallelism by domain decomposition.** Six independent fronts become six concurrent agents, so the research runs at once instead of in sequence, and traceability holds because every claim carries a source.
- **Scope locked before execution.** A requirements interview (markets, depth, format) up front avoids rework and keeps the deliverable aimed at an executive reader.
- **Honest handling of collection limits (anti-slop).** Large marketplaces block scraping (HTTP 403), so automatic listing counts were marked non-measurable, and paid import databases were flagged as a known gap.
- **Open items tracked.** Certifications and tax rates to confirm with a customs broker were logged as "to validate," separating conclusion from assumption.

## Result

The pipeline produced a consolidated executive report crossing the six fronts with the financial model. The recommendation was a conditional GO, based on the pilot container's margin and return, combined with a positioning thesis (enter as the best-price option in the region, differentiated on local warranty and support) and a market-by-market entry sequence guided by the regulatory window. It included running one pilot container to validate real turnover and certification before scaling. The specific financial indicators that back the decision are confidential and aren't part of the public material.

> Sanitized: no company identity, no business figures (margin, ROI, break-even, cost/revenue), no internal purchase prices or per-competitor deltas. The 1–3 year projections were unvalidated assumptions to calibrate, included here to show the method.

---

# Estudo de mercado multi-agente

Um pipeline de pesquisa que decide se vale importar e revender uma nova linha de produto, tocado por um orquestrador e uma frota de agentes de pesquisa em paralelo, cada um citando as fontes.

**Papel:** desenhei e orquestrei o pipeline inteiro (escopo, decomposição do problema, papéis dos agentes, a camada financeira determinística e o relatório consolidado).
**Stack:** Claude (Claude Code) como orquestrador multi-agente · Python + openpyxl para o modelo financeiro determinístico · pesquisa web com captura de fontes · relatório HTML autocontido.

## O problema

A empresa precisava decidir se importava e revendia uma linha de estações portáteis de energia em dois mercados latino-americanos. A decisão dependia de seis frentes muito distintas (tamanho e crescimento de mercado, concorrentes, preço em marketplace, canais e marketing, ambiente regulatório-tributário e um benchmark competitivo), mais a parte financeira de um container-piloto. Pesquisar essas frentes no braço é lento e desconexo. O objetivo foi um pipeline que rodasse as frentes em paralelo, com fontes citadas, e consolidasse tudo num relatório executivo.

## A arquitetura

Um orquestrador com frota fan-out/fan-in, e a fonte de verdade separada pela natureza do dado.

- **Camada determinística (sem LLM).** O núcleo financeiro e de importação (custos, preços, break-even) é extraído e calculado de uma planilha com Python/openpyxl e escrito num arquivo de findings. Os números que sustentam a decisão ficam fora do alcance do modelo. O LLM raciocina sobre o dado; não o inventa.
- **Seis agentes de pesquisa em paralelo,** um por frente externa, cada um encarregado de coletar e citar fontes: tamanho e crescimento de mercado; concorrentes e posicionamento; preço em marketplace; cliente-alvo, canais e marketing; ambiente regulatório-tributário (imposto de importação, certificações obrigatórias, classificação fiscal); e um benchmark competitivo consolidado.
- **Orquestração.** O coordenador trava o escopo com o stakeholder por perguntas estruturadas (mercados, formato, profundidade), dispara os agentes de forma concorrente e, no fan-in, junta os achados de cada frente com o bloco financeiro determinístico num único relatório HTML executivo cobrindo mercado, concorrência, SWOT, benchmark, regulatório, projeção de 1–3 anos e fontes.

## Destaques de engenharia

- **Dado determinístico × dado pesquisado, separados.** Números críticos calculados em código; narrativa e contexto de mercado pesquisados pelo LLM. Essa separação é o que mantém a alucinação longe dos pontos em que a decisão se apoia.
- **Paralelismo real por decomposição de domínio.** Seis frentes independentes viram seis agentes concorrentes, então a pesquisa roda de uma vez em vez de em sequência, e a rastreabilidade se mantém porque cada afirmação vem com fonte.
- **Escopo travado antes de executar.** Uma entrevista de requisitos (mercados, profundidade, formato) no começo evita retrabalho e mantém o entregável mirado no leitor executivo.
- **Tratamento honesto dos limites de coleta (anti-slop).** Marketplaces grandes bloqueiam scraping (HTTP 403), então a contagem automática de anúncios foi marcada como não-mensurável, e bases pagas de importação ficaram sinalizadas como lacuna conhecida.
- **Pendências rastreadas.** Certificações e alíquotas a confirmar com o despachante ficaram como "a validar", separando conclusão de premissa.

## Resultado

O pipeline produziu um relatório executivo consolidado cruzando as seis frentes com o modelo financeiro. A recomendação foi um GO condicional, baseado na margem e no retorno do container-piloto, combinado com uma tese de posicionamento (entrar como a opção de melhor preço na região, diferenciada em garantia e suporte local) e uma sequência de entrada mercado a mercado guiada pela janela regulatória. Incluiu rodar um container-piloto para validar giro real e certificação antes de escalar. Os indicadores financeiros específicos que sustentam a decisão são confidenciais e não fazem parte do material público.

> Sanitizado: sem identificação da empresa, sem números de negócio (margem, ROI, break-even, custo/faturamento), sem preços de compra internos ou deltas por concorrente. As projeções de 1–3 anos eram premissas a calibrar, não validadas, incluídas aqui para mostrar o método.
