# Executive dashboards

*Português abaixo.*

A suite of C-level dashboards that gave company leadership its first single, always-current view of the operation.

**Role:** end to end (problem discovery with each area, architecture, ETL and rendering code, deploy, and presenting the consolidated view to leadership).
**Stack:** Python (openpyxl, requests) · self-contained HTML with inline SVG and Chart.js · FastAPI + SQLite for the ones that need auth/state · Microsoft Graph (app-only) · Docker · scheduled rebuilds.

## The problem

Management KPIs lived in the ERP, in spreadsheets on SharePoint, and in desktop-only BI files, all at once. Finance, foreign trade, HR, and the operations areas each kept their own reports, in their own format and cadence. Leadership had no single place to read the health of the operation or track targets against actuals. The existing reports were also static and depended on one person running the refresh on their own machine, so when that machine was off the numbers froze.

## The architecture

One pattern, reused across every dashboard:

> corporate source (SharePoint / ERP / internal API) → Python ETL → self-contained HTML → deploy with a daily automatic sync

- **Extract.** Python scripts pull from spreadsheets and internal APIs. For SharePoint I read files server-side through Microsoft Graph (app-only), so nothing depends on a specific person's laptop being on.
- **Render.** The script normalizes the data and writes a single self-contained HTML file, with charts as inline SVG (print-safe, no JS) or embedded Chart.js when it needs to be interactive.
- **Deploy.** Static dashboards ship as nginx; the ones that need login or per-user state run as a FastAPI app with a persistent volume for SQLite. A shared auth module (PBKDF2 + HttpOnly session cookie) is reused across them.
- **Refresh.** A scheduled task rebuilds and republishes daily, and only commits when the content actually changed. An optional "Refresh" button lets anyone reprocess on demand from any computer.

If a source is down, the dashboard still builds and marks that indicator as unavailable. It never takes the whole page down with it.

## Engineering highlights

- **No more single-machine dependency.** The daily scheduled rebuild removed the "only person X can update it" bottleneck.
- **Multi-entity, multi-currency (BRL / USD / CLP).** Several group entities and countries consolidated in one place, with a heuristic that catches values arriving in the wrong currency and normalizes them.
- **Stable annotation keys.** User annotations (data validations) are anchored to business fields, not array position, so updating the source spreadsheet doesn't detach notes already made.
- **C-level reading.** Each card carries value, target, % attainment, a status light, and a sparkline with the target line. A "health by area" layer collapses dozens of KPIs into one legible verdict.
- **Shared state.** Validations and "updated by X" persist on the server and are visible to the whole team through the URL, ending the download-JSON-and-reprocess-locally loop.

## Result

Four dashboards in production, plus a top-level panel that aggregates KPIs from up to eight areas onto one screen, on a daily automatic cadence with on-demand refresh. It gave leadership the first consolidated view of the operation, replacing manual processes tied to one machine.

> Sanitized for public use: no company identity, real figures, names, internal URLs, or credentials. Architecture and technique only.

---

# Dashboards executivos

Uma suíte de dashboards C-level que deu à liderança da empresa a primeira visão única e sempre atualizada da operação.

**Papel:** ponta a ponta (descoberta do problema com cada área, arquitetura, código de ETL e geração, deploy, e apresentação da visão consolidada para a liderança).
**Stack:** Python (openpyxl, requests) · HTML autocontido com SVG inline e Chart.js · FastAPI + SQLite nos que precisam de auth/estado · Microsoft Graph (app-only) · Docker · rebuild agendado.

## O problema

Os indicadores de gestão viviam no ERP, em planilhas no SharePoint e em arquivos de BI que só abriam no desktop, tudo ao mesmo tempo. Financeiro, comércio exterior, RH e as áreas de operação mantinham cada um seu relatório, no próprio formato e cadência. A liderança não tinha um lugar único para ler a saúde da operação nem acompanhar meta contra realizado. Os relatórios existentes também eram estáticos e dependiam de uma pessoa rodar a atualização na própria máquina, então com a máquina desligada o dado congelava.

## A arquitetura

Um padrão só, reaproveitado em todos os dashboards:

> fonte corporativa (SharePoint / ERP / API interna) → ETL em Python → HTML autocontido → deploy com sync diário automático

- **Extração.** Scripts Python puxam de planilhas e APIs internas. No SharePoint, leio os arquivos direto do servidor via Microsoft Graph (app-only), então nada depende do notebook de uma pessoa estar ligado.
- **Geração.** O script normaliza os dados e escreve um único HTML autocontido, com gráficos em SVG inline (seguro para impressão, sem JS) ou Chart.js embutido quando precisa ser interativo.
- **Deploy.** Dashboards estáticos sobem como nginx; os que precisam de login ou estado por usuário rodam como app FastAPI com volume persistente para SQLite. Um módulo de auth compartilhado (PBKDF2 + cookie de sessão HttpOnly) é reusado entre eles.
- **Atualização.** Uma tarefa agendada regenera e republica todo dia, e só faz commit quando o conteúdo mudou de fato. Um botão "Atualizar" opcional deixa qualquer pessoa reprocessar sob demanda de qualquer computador.

Se uma fonte cai, o dashboard gera mesmo assim e marca aquele indicador como indisponível. Nunca derruba a página inteira junto.

## Destaques de engenharia

- **Fim da dependência de uma máquina.** O rebuild diário agendado tirou o gargalo do "só a pessoa X atualiza".
- **Multi-empresa, multi-moeda (BRL / USD / CLP).** Várias entidades do grupo e países consolidados num lugar só, com uma heurística que pega valores que chegam na moeda errada e os normaliza.
- **Chave de anotação estável.** As anotações do usuário (validações de dados) são ancoradas em campos de negócio, não na posição do array, então atualizar a planilha-fonte não descola as notas já feitas.
- **Leitura C-level.** Cada cartão traz valor, meta, % de atingimento, um farol e um sparkline com a linha de meta. Uma camada de "saúde por área" resume dezenas de KPIs num veredito legível.
- **Estado compartilhado.** Validações e "atualizado por X" ficam no servidor e são vistos por toda a equipe pela URL, encerrando o ciclo de baixar-JSON-e-reprocessar-na-mão.

## Resultado

Quatro dashboards em produção, mais um painel de topo que junta KPIs de até oito áreas numa tela só, em cadência diária automática com refresh sob demanda. Deu à liderança a primeira visão consolidada da operação, no lugar de processos manuais presos a uma máquina.

> Sanitizado para uso público: sem identificação da empresa, números reais, nomes, URLs internas ou credenciais. Só arquitetura e técnica.
