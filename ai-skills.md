# AI tooling / agent skills

*Português abaixo.*

I run most of my work through coding agents (Claude Code and others), and over time I built and extended a set of *skills*, which are packaged instructions and small tools that make an agent do a specific job well. This page is honest about what I authored and what I extended from someone else's work. I keep the credit explicit, because a technical reviewer will check.

## Built by me

Skills and subagents I wrote to automate my own workflow. They're tied to internal tooling, so they're described by function, not by internal detail.

- **Dashboard generator.** Regenerates and validates HTML dashboards from their sources, following a fixed environment convention (temp copy of the source, encoding, headless screenshot check).
- **Deploy skill.** One-command deploy of an app to a container platform (local package → Git repo → auto-sync → service with env/volume/port), validated end to end by an HTTP 200 check.
- **Daily briefing.** Reads task and reminder lists and formats a daily briefing.
- **Weekly skill scanner.** A routine that scans for new agent skills, plugins, and MCP servers worth adopting.
- **Two subagents.** A visual dashboard reviewer (headless screenshot + a layout/overflow/contrast checklist) and a deploy verifier with built-in guardrails and a pass/fail checklist.
- **PDF triage.** Classifies a PDF (native text vs. scanned) before any extraction or OCR, so extraction is routed the cheap, local, privacy-safe way when possible. It wraps `pdf-inspector` (by Firecrawl) and complements Anthropic's `pdf` skill.

## Extended from a public base (credited)

These started from someone else's open-source project. I extended them and kept the attribution explicit.

- **humanizer-pt-br.** A fork/extension of the `humanizer-pt-br` skill by **mackswendhell** (MIT), which is itself based on Wikipedia's *Signs of AI writing* (WikiProject AI Cleanup). My contribution is the Brazilian-Portuguese calibration and a set of 2026 tells (uniform sentence cadence, empty intensifier adverbs, video-style staccato), plus alert-word updates and a false-positive note. It removes AI writing tics from Portuguese text.
- **spec-driven-audit.** A complementary skill that ports three concepts from **onp-spec** (by Vitor Manoel, MIT): requirement→task→test traceability with orphan detection, a definition-of-done in Given/When/Then where only a passing test counts as proof, and blocking assumption/open-question sections in the spec. Credit to onp-spec; the base spec-driven framework it complements is a third-party skill I use.

## Tools I use (not mine)

For completeness, here are third-party skills and tools I rely on but did not write, so they don't belong in the two sections above: superpowers, Anthropic's document skills, graphify, the tlc-spec-driven framework, onp-spec, the Vercel skill set, and the Playwright MCP.

---

# AI tooling / agent skills

Toco a maior parte do meu trabalho por agentes de código (Claude Code e outros) e, com o tempo, criei e estendi um conjunto de *skills*, que são instruções empacotadas e pequenas ferramentas que fazem um agente executar bem uma tarefa específica. Esta página é honesta sobre o que é autoria minha e o que estendi do trabalho de outra pessoa. Mantenho o crédito explícito porque um revisor técnico confere.

## Criadas por mim

Skills e subagentes que escrevi para automatizar meu próprio fluxo. São coladas a ferramental interno, então descrevo pela função, não pelo detalhe interno.

- **Gerador de dashboards.** Regenera e valida dashboards HTML a partir das fontes, seguindo uma convenção fixa de ambiente (cópia temporária da fonte, encoding, checagem por screenshot headless).
- **Skill de deploy.** Deploy de um app numa plataforma de containers em um comando (pacote local → repo Git → auto-sync → serviço com env/volume/porta), validado ponta a ponta por HTTP 200.
- **Briefing diário.** Lê listas de tarefas e lembretes e formata um briefing do dia.
- **Varredura semanal de skills.** Uma rotina que procura novas skills de agente, plugins e servidores MCP que valem adotar.
- **Dois subagentes.** Um revisor visual de dashboards (screenshot headless + checklist de layout/overflow/contraste) e um verificador de deploy com guardrails embutidos e checklist de passou/falhou.
- **Triagem de PDF.** Classifica um PDF (texto nativo vs. escaneado) antes de qualquer extração ou OCR, para rotear a extração pelo caminho barato, local e seguro para privacidade quando dá. Envolve o `pdf-inspector` (da Firecrawl) e complementa a skill `pdf` da Anthropic.

## Estendidas de uma base pública (com crédito)

Estas partiram do projeto open-source de outra pessoa. Eu estendi e mantive a atribuição explícita.

- **humanizer-pt-br.** Um fork/extensão da skill `humanizer-pt-br` do **mackswendhell** (MIT), que por sua vez se baseia no *Signs of AI writing* da Wikipedia (WikiProject AI Cleanup). Minha contribuição é a calibração para português brasileiro e um conjunto de tells de 2026 (cadência uniforme das frases, advérbios de ênfase vazios, staccato de vídeo), mais atualização de palavras-alerta e uma nota anti-falso-positivo. Remove tiques de escrita de IA em texto em português.
- **spec-driven-audit.** Uma skill complementar que porta três conceitos do **onp-spec** (de Vitor Manoel, MIT): rastreabilidade requisito→task→teste com detecção de órfãos, um definition-of-done em Given/When/Then onde só teste que passa conta como prova, e seções bloqueantes de premissa/pergunta-aberta na spec. Crédito ao onp-spec; o framework spec-driven base que ela complementa é uma skill de terceiro que eu uso.

## Ferramentas que eu uso (não minhas)

Por completude, estas são skills e ferramentas de terceiros das quais dependo mas que não escrevi, então não entram nas duas seções acima: superpowers, as document skills da Anthropic, graphify, o framework tlc-spec-driven, o onp-spec, o conjunto de skills da Vercel e o MCP do Playwright.
