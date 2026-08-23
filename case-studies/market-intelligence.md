# Market intelligence platform

*Português abaixo.*

A competitive-intelligence platform that measures market share per brand from public data, cross-checked against internal sell-in — built to replace a paid comex tool that nobody wanted to buy.

**Role:** sole author — source discovery, spec, data pipeline, backend, frontend, tests, deploy, and an adversarial audit of my own results.
**Stack:** Python · DuckDB over Parquet · FastAPI · APScheduler · vanilla JS with hand-built inline SVG charts.

## The problem

The company needed to know how much each competing brand sells, where it gains or loses ground, and how its own position moves — without paying for a comex tool. One fact shaped the whole design: import data by company is not public in Brazil. So instead of "who imported," I measured what was actually installed, by manufacturer, from open regulatory data, and cross-checked it with internal sell-in.

The second goal mattered as much as the numbers: methodological rigor. Public sources publish with a lag and at different granularities, and a naive comparison flipped the sign of the conclusion more than once.

## The architecture

**Sources.** Open regulatory data on distributed generation (two public Parquet files — one technical with manufacturer and model, one registry-level with region and tariff group — joined on the project key). Aggregate import data by tariff code for segments the first source doesn't cover by brand. A manufacturer certification catalog. And internal sell-out/sell-in sheets, read live from SharePoint through Microsoft Graph — never versioned in the repo.

**ETL.** `fetch` downloads only when the local file is missing or stale, validates the schema by reading just the Parquet metadata (not the data), and swaps the file atomically, keeping the last good copy if a download comes back corrupt. `normalize` turns free-text manufacturer names (`197 - GROWATT`, `SOFAR SOLAR`, `NÃO HÁ`) into canonical brands, matching on word boundaries — never substrings — so `SWEGON` doesn't collapse into `WEG`, and handling rebadged products confirmed by model-string evidence.

**The performance decision.** Aggregation runs directly on the Parquet in two passes, so it fits a small container: DuckDB aggregates ~4.5M rows by raw manufacturer and size band down to a few thousand rows in SQL, then Python applies brand normalization only on that small result. No per-row Python UDF over 4.5M records, and the full base never lands in memory.

**Serving.** A minimal FastAPI serves the static data-driven front and the JSON. APScheduler refreshes the public data monthly (with boot-if-stale) and the internal data daily, so the container re-downloads and reprocesses on its own. The charts are inline SVG built by hand in vanilla JS, with a small trilingual i18n (pt/en/zh).

## Engineering highlights

- **4.5M rows in a ~132 MB container.** The trick isn't brute force — it's DuckDB aggregating the Parquet in SQL before Python touches the data. A naive in-container refresh would have been OOM-killed. That constraint shaped the design.
- **Open data instead of paid import intelligence.** Reconstructing per-competitor share from what was installed, with import and certification data covering what the main source can't see.
- **Robust brand matching.** Free-text normalization on word boundaries, rebadge detection by model-string evidence, and model → product-family matching (~80%) against the internal catalog.
- **Temporal-window rigor — the strongest finding.** The regulator publishes by homologation date, so recent months come in under-counted. The comparison cut is `min(last internal month, last mature regulator month)`, with maturity auto-calibrated from the worst closed month of prior years. Without it, a naive window manufactured several points of "market decline" in the wrong direction. It also caught the bias of showing ~13 years of cumulative total as if it were the current position.
- **Adversarial self-audit.** 54 findings verified one by one — label/UI, bug, methodology, source limit — with explicit refutation of the ones that didn't hold up.
- **Reusable production hardening.** Full login gate, nonce CSP, persistent rate limiting, CAPTCHA, an online backup of the accounts database, and an access audit trail with an LGPD note.

## Results

- ~4.5M systems processed; the source Parquet is ~103 MB per refresh.
- The container runs in ~132 MB of RAM.
- ~500 tests (up from ~45 at MVP).
- Monthly automated refresh of public data plus daily refresh of internal data.
- Manufacturer identification covers ~97% of the base; the unit-power extractor covers ~77% with ~92% accuracy against an oracle.

On the business side — without figures — the platform showed that an earlier reading had understated the brand's position by mixing time windows; once the window was fixed, the competitive position in the commercial segment was much stronger than leadership had seen.

> Sanitized: no company or partner-brand identity, no competitor names or shares, no real sell-in/sell-out, no secrets. Architecture and technique only.

---

# Plataforma de inteligência de mercado

Uma plataforma de inteligência competitiva que mede share por marca a partir de dados públicos, cruzando com o sell-in interno — feita para substituir uma ferramenta paga de comex que ninguém queria contratar.

**Papel:** autor único — descoberta das fontes, spec, pipeline de dados, backend, frontend, testes, deploy e uma auditoria adversarial dos próprios resultados.
**Stack:** Python · DuckDB sobre Parquet · FastAPI · APScheduler · JS vanilla com gráficos SVG inline feitos à mão.

## O problema

A empresa precisava saber quanto cada marca concorrente vende, onde ganha ou perde espaço e como a própria posição se move — sem pagar por uma ferramenta de comex. Um fato moldou todo o desenho: dado de importação por empresa não é público no Brasil. Então, em vez de "quem importou", medi o que foi de fato instalado, por fabricante, a partir de dados abertos regulatórios, e cruzei com o sell-in interno.

O segundo objetivo pesou tanto quanto os números: rigor metodológico. As fontes públicas publicam com defasagem e em granularidades diferentes, e uma comparação ingênua inverteu o sinal da conclusão mais de uma vez.

## A arquitetura

**Fontes.** Dados abertos regulatórios de geração distribuída (dois arquivos Parquet públicos — um técnico com fabricante e modelo, um cadastral com região e grupo tarifário — casados pela chave do empreendimento). Dado agregado de importação por código tarifário para os segmentos que a primeira fonte não cobre por marca. Um catálogo de certificação de fabricantes. E planilhas internas de sell-out/sell-in, lidas ao vivo do SharePoint via Microsoft Graph — nunca versionadas no repo.

**ETL.** O `fetch` baixa só quando o arquivo local está ausente ou velho, valida o schema lendo apenas os metadados do Parquet (não os dados) e troca o arquivo de forma atômica, preservando a última cópia boa se o download vier corrompido. O `normalize` transforma o nome de fabricante em texto livre (`197 - GROWATT`, `SOFAR SOLAR`, `NÃO HÁ`) em marca canônica, casando por fronteira de palavra — nunca substring — para que `SWEGON` não vire `WEG`, e tratando rebadge confirmado por evidência de string de modelo.

**A decisão de performance.** A agregação roda direto sobre o Parquet em duas fases, para caber num container pequeno: o DuckDB agrega ~4,5M de linhas por fabricante bruto e faixa de porte até uns poucos milhares de linhas em SQL, depois o Python aplica a normalização de marca só nesse resultado pequeno. Sem UDF Python por linha sobre 4,5M de registros, e a base inteira nunca entra em memória.

**Serviço.** Um FastAPI mínimo serve o front estático data-driven e o JSON. O APScheduler atualiza o dado público mensalmente (com boot-if-stale) e o interno diariamente, então o container rebaixa e reprocessa sozinho. Os gráficos são SVG inline montado à mão em JS vanilla, com um i18n próprio trilíngue (pt/en/zh).

## Destaques de engenharia

- **4,5M de linhas num container de ~132 MB.** O truque não é força bruta — é o DuckDB agregando o Parquet em SQL antes de o Python tocar no dado. Um refresh ingênuo dentro do container teria sido OOM-killed. Essa restrição moldou o desenho.
- **Dados abertos no lugar de inteligência de importação paga.** Reconstruir o share por concorrente a partir do que foi instalado, com dado de importação e certificação cobrindo o que a fonte principal não vê.
- **Matching de marca robusto.** Normalização de texto livre por fronteira de palavra, detecção de rebadge por evidência de string de modelo, e casamento modelo → família de produto (~80%) contra o catálogo interno.
- **Rigor de janela temporal — o achado mais forte.** O regulador publica por data de homologação, então os últimos meses vêm subcontados. O corte de comparação é `mín(último mês interno, último mês maduro do regulador)`, com a maturidade auto-calibrada pelo pior mês fechado dos anos anteriores. Sem isso, uma janela ingênua fabricava vários pontos de "queda de mercado" na direção errada. Também pegou o viés de mostrar ~13 anos de acumulado como se fosse a posição atual.
- **Auditoria adversarial do próprio painel.** 54 achados verificados um a um — rótulo/UI, bug, metodologia, limite de fonte — com refutação explícita dos que não se sustentaram.
- **Hardening de produção reusável.** Gate de login total, CSP com nonce, rate-limit persistente, CAPTCHA, backup online do banco de contas e uma trilha de auditoria de acesso com nota de LGPD.

## Resultados

- ~4,5M de sistemas processados; o Parquet de origem tem ~103 MB por refresh.
- O container roda em ~132 MB de RAM.
- ~500 testes (partindo de ~45 no MVP).
- Refresh mensal automatizado do dado público mais refresh diário do dado interno.
- A identificação de fabricante cobre ~97% da base; o extrator de potência unitária cobre ~77% com ~92% de acerto contra um oráculo.

No lado de negócio — sem cifras — a plataforma mostrou que uma leitura anterior subestimava a posição da marca por misturar janelas temporais; corrigida a janela, a posição competitiva no segmento comercial era bem mais forte do que a diretoria enxergava.

> Sanitizado: sem identificação da empresa ou da marca parceira, sem nomes de concorrentes ou shares, sem sell-in/sell-out real, sem segredos. Só arquitetura e técnica.
