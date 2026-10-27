# Microgrid simulator with measured parity

*Português abaixo.*

An in-house simulator for sizing microgrids (solar, battery storage, generator, wind, grid connection) that reproduces the results of the commercial design tool the engineering team licenses, and adds what that tool never had: Brazilian electricity tariffs. It started on 13 October 2026 and is in active development.

**Role:** sole engineer (scoping, specs, simulation engine, importer, parity harness, the web screens and the tests).
**Stack:** Python 3.14 · NumPy · Pydantic v2 · FastAPI · vanilla JS · pytest · Playwright.

## The problem

Engineering sizes microgrid projects in a licensed desktop tool, and the company wants to stop depending on it. The tool can model the green and blue A4 tariff structures, but not Brazil's net-metering compensation rules, tariff flags or cascading taxes. The goal wasn't "a simulator". It was a simulator whose numbers engineering could trust as much as the one they already knew, so every rule had to be checked against that tool's own results.

## How parity works

- **The reference tool is the answer key.** Its sample projects ship with both the inputs and the computed results. An importer reads the project format, the engine recomputes it, and a parity harness compares the two candidate by candidate. Where the saved results came from an older engine, I re-exported them from the current version and used those instead.
- **Tolerance in steps, decided up front.** Grid-only results within ±0.01%; with storage, demand and energy within ±2%, battery throughput within ±5% and net present cost within ±1%. Hourly comparisons have per-column tolerances, and a NaN fails.
- **When the annual total doesn't close, ask for the monthly breakdown.** A US utility tariff with critical-peak events sat at −5% on energy after many hypotheses. One monthly bill from the reference tool exposed five rules that cancelled each other out in the annual total: daylight saving time, how holidays apply, how the event calendar moves to the simulated year, an inclusive end hour, and a fixed charge counted inside the energy line. After that the gap was −6.6e-9.

## Engineering highlights

- **Missing field means error.** The importer never fills a gap with a default I made up. Absence is only accepted where its meaning was measured. It took three rounds of audit finding invented defaults one at a time; now the rule is in the first spec of every milestone.
- **Right KPI, wrong configuration.** The net present cost matched on all 3,044 candidates of the first milestone, and some of them were still linked to the wrong battery model. Since then the candidate key has to include every choice between alternatives, and a uniqueness test (same key, same results) guards it.
- **An allowlist of known deviations needs a ceiling.** Each accepted deviation carries a maximum size relative to the reference and the expected sign, so one that keeps growing, flips sign or turns into NaN fails the gate.
- **Out-of-scope effects are declared, not absorbed.** Multi-year load growth, demand response, outages and incentives change the reference numbers. The importer detects them in the input and the parity is excluded per metric, so a project with an incentive still counts for dispatch.
- **Specs with traceable acceptance criteria, mutation testing and independent auditors.** Each milestone has numbered acceptance criteria tagged in the tests, mutants killed to show the tests can fail, and auditors that recompute the numbers from the raw source before a milestone is called done.

## Results (as of October 2026)

- 4,162 automated tests on the main branch (24 October 2026).
- Net present cost matched the results stored in the sample projects on all 3,044 candidates of the first milestone. Against the current version of the reference engine, NPC still differed by +3.5% to +20.3% on the 146 eligible candidates of the battery-comparison sample, measured before a 19 October battery-life fix that still has to be re-measured, so it isn't part of the parity gate yet.
- A real Brazilian utility bill (green A3a tariff) reproduced within 0.00002%, including taxes calculated in cascade.
- Covered so far: grid, solar, idealized and kinetic battery models, generator, wind and sensitivity analysis, plus Brazilian tariffs. Still open: hydro, combined heat and power, multi-year, thermal loads, hydrogen and some economic options.

> Sanitized: no company identity, client projects, prices or proprietary code. The reference tool isn't named, and every number above comes from re-exports and engine runs over its public sample projects, plus one real bill cited without any identifying data.

---

# Simulador de microrredes com paridade medida

Um simulador próprio para dimensionar microrredes (solar, bateria, gerador, eólica, conexão à rede) que reproduz os resultados da ferramenta comercial de projeto que a engenharia licencia, e acrescenta o que ela nunca teve: tarifa brasileira. Começou em 13 de outubro de 2026 e está em desenvolvimento.

**Papel:** engenheiro único (escopo, specs, motor de simulação, importador, harness de paridade, as telas web e os testes).
**Stack:** Python 3.14 · NumPy · Pydantic v2 · FastAPI · JS vanilla · pytest · Playwright.

## O problema

A engenharia dimensiona projetos de microrrede numa ferramenta desktop licenciada, e a empresa quer deixar de depender dela. A ferramenta modela a estrutura das tarifas verde e azul A4, mas não a compensação da geração distribuída, as bandeiras nem os tributos em cascata. O objetivo não era "um simulador". Era um simulador em cujos números a engenharia confiasse tanto quanto no que já conhecia, então cada regra tinha de ser conferida contra os resultados da própria ferramenta.

## Como a paridade funciona

- **A ferramenta de referência é o gabarito.** Os projetos de exemplo dela vêm com a entrada e o resultado calculado. Um importador lê o formato do projeto, o motor recalcula, e um harness de paridade compara os dois candidato a candidato. Onde o resultado salvo vinha de um motor antigo, reexportei pela versão atual e usei o novo.
- **Tolerância em degraus, decidida antes.** Só rede: ±0,01%. Com bateria: demanda e energia ±2%, vazão da bateria ±5% e custo presente líquido ±1%. A comparação hora a hora tem tolerância por coluna, e NaN reprova.
- **Quando o total anual não fecha, pedir a quebra mensal.** Uma tarifa americana com eventos de ponta crítica ficou em −5% na energia depois de muitas hipóteses. Uma única fatura mensal da ferramenta de referência expôs cinco regras que se cancelavam no total do ano: horário de verão, como o feriado se aplica, como o calendário de eventos se desloca para o ano simulado, a hora final inclusiva e uma tarifa fixa contada dentro da linha de energia. Depois disso a diferença foi para −6,6e-9.

## Destaques de engenharia

- **Campo ausente é erro.** O importador nunca preenche lacuna com um default inventado por mim. Ausência só é aceita onde o significado dela foi medido. Foram três rodadas de auditoria achando default inventado um por um; hoje a regra está na primeira spec de todo marco.
- **KPI certo, configuração errada.** O custo presente líquido batia nos 3.044 candidatos do primeiro marco, e parte deles continuava ligada ao modelo de bateria errado. Desde então a chave do candidato inclui toda escolha entre alternativas, e um teste de unicidade (mesma chave, mesmo resultado) protege isso.
- **Lista de desvios aceitos precisa de teto.** Cada desvio aceito leva um tamanho máximo relativo à referência e o sinal esperado, então um que continua crescendo, troca de sinal ou vira NaN reprova o gate.
- **Efeito fora do escopo é declarado, não absorvido.** Crescimento de carga multi-ano, resposta da demanda, quedas e incentivos mudam os números de referência. O importador detecta isso na entrada e a paridade é excluída por métrica, então um projeto com incentivo continua valendo para o despacho.
- **Specs com critérios de aceite rastreáveis, teste de mutação e auditores independentes.** Cada marco tem critérios de aceite numerados e marcados nos testes, mutantes mortos para mostrar que os testes conseguem falhar, e auditores que recalculam os números da fonte bruta antes de o marco ser dado como pronto.

## Resultados (outubro de 2026)

- 4.162 testes automatizados na branch principal (24 de outubro de 2026).
- Custo presente líquido batendo com o resultado gravado nos projetos de exemplo, nos 3.044 candidatos do primeiro marco. Contra a versão atual do motor de referência, o VPL ainda diferia de +3,5% a +20,3% nos 146 candidatos elegíveis do caso de comparação de baterias, medido antes de uma correção na vida da bateria em 19 de outubro que ainda precisa ser remedida, por isso não entra no gate de paridade.
- Uma fatura real de distribuidora brasileira (tarifa verde A3a) reproduzida a 0,00002%, com os tributos em cascata.
- Coberto até agora: rede, solar, bateria idealizada e cinética, gerador, eólica e análise de sensibilidade, mais a tarifa brasileira. Em aberto: hidro, cogeração, multi-ano, carga térmica, hidrogênio e algumas opções econômicas.

> Sanitizado: sem identificação da empresa, projetos de cliente, preços ou código proprietário. A ferramenta de referência não é nomeada, e todo número acima vem de reexports e rodadas do motor sobre os projetos de exemplo públicos dela, mais uma fatura real citada sem nenhum dado que a identifique.
