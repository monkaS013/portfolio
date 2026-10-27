# Ask-HR: a RAG assistant that ran in production, and why I turned it off

*Português abaixo.*

An in-portal assistant that answered employees' HR questions in natural language, citing the source document. Its widget was in the HR platform's interface from 21 August to 24 September 2026, for the people with access to it, and it was built to stay away from sensitive data. I took the widget out during an audit round; the full page kept answering until 9 October, when I replaced it with a help center that needs no LLM and switched the LLM path off. The last section covers that.

**Role:** sole author, end to end (spec, architecture, the Python RAG sidecar, the eval harness, and the production deploy).
**Stack:** Python · LangGraph · pgvector (HNSW) · local embeddings (fastembed, multilingual-e5-large) · Claude Haiku · FastAPI · Postgres.

## The problem

A company's staff kept asking HR the same handful of things about benefits and policies, all of it buried in documents nobody wanted to dig through, and the answers came back slow and inconsistent. I wanted an assistant that replies in plain language and shows where the answer came from, without ever indexing salaries, performance reviews, or any personal data.

## The architecture

It follows a sidecar pattern. The HR app (Next.js) hands the retrieval work to an isolated Python service over an internal, token-authenticated HTTP call. The retrieval logic itself is a LangGraph state machine:

```
retrieve → grade → route → generate | fallback
```

- **retrieve** embeds the question locally and pulls the top-k chunks from pgvector by cosine similarity.
- **grade** asks the model which chunks are actually relevant, plus a confidence score (structured output, JSON schema).
- **route** picks the next step. Enough confidence and kept chunks → generate; otherwise → fallback.
- **generate** answers only from the retrieved context, citing the document by name. No grounded citation → it becomes a fallback.
- **fallback** says "I couldn't find that." instead of guessing.

Embeddings run locally (ONNX, e5-large, 1024 dims), so document text never leaves the infrastructure to be vectorized. Vectors live in the same Postgres as the app, behind an HNSW cosine index created through a hand-edited Prisma migration (the `<=>` operator runs in raw SQL, since the ORM doesn't expose it).

## Engineering highlights

- **Gold-standard eval as a release gate.** 16 real HR questions plus PII probes. The build fails if hit-rate@5 drops below the threshold, or if any PII probe leaks instead of returning a fallback. The metrics are pure functions, testable on their own.
- **Guardrails mapped to the OWASP LLM Top 10.** Retrieved context is delimited and treated as data, not instructions (prompt injection). Ingestion strips zero-width and bidi characters, the kind of hidden instruction a human never sees. Question length and upload size are capped. A CPF/CNPJ heuristic warns the admin at ingestion time.
- **Grounded citations only.** A citation is accepted only if the document exists in the corpus; an answer with no grounded citation is demoted to a fallback automatically.
- **RBAC and LGPD by design.** Login required, ingestion limited to HR/admin, only non-sensitive institutional documents indexed, and only the retrieved snippet plus the question ever reach the LLM provider.
- **Data isolation proven by test.** The sidecar only ever touches the two RAG tables. A test scans the service's SQL to prove no operational table is referenced and no secret is hard-coded. Everything comes from environment variables.

## Results

About five weeks visible in the interface for the people with access. On the (fictional) eval set, hit-rate@5 is 1.0 across the 16 questions, which is a small hand-built set. I gate on hit-rate rather than precision on purpose. The eval corpus has few chunks per document, so precision would read low and mean little there. Warm latency is around 2.4–4s on CPU, with the cold start removed by warming the model at boot. Embeddings cost nothing per token, and the vectors sit in a database that already existed.

## What replaced it

With the assistant out of the interface, people still needed a way to find answers, and I didn't want a model call on every question. So I built a help center that answers by search alone. It starts as a lexical search in TypeScript inside the app (light stemming, typo tolerance, per-field BM25 and a synonym dictionary) over the system's help articles and the HR policy base. It then fuses those results with local embeddings from the same multilingual model the RAG used, through reciprocal rank fusion. No LLM generates anything, and if the embedding service is down or slower than 800 ms the search falls back to lexical only. Each article follows the same module visibility as the menu, so people only see help for what they can access.

I measured it on a blind set of 144 questions, written before any tuning by an agent that hadn't read the articles or the engine. On the 114 questions that have an answer, the right one landed in the top 3 for 104 of them (91.2%), against 71.9% for lexical search alone; colloquial phrasing went from 33.3% to 80.0%. Of the 30 questions with no answer, 90% come back flagged as such. I didn't run the old RAG on this set, so this isn't a head-to-head comparison. The LLM path now sits behind a flag that is off, which also ended the inference cost. The RAG code is still in the repo; [rag-docs](https://github.com/monkaS013/rag-docs) is the open version of the same pipeline.

> Sanitized for public use: no company identity, real names, internal hosts, or credentials. The eval corpus is fictional by design.

---

# Pergunte ao RH: um assistente RAG que rodou em produção, e por que eu desliguei

Um assistente dentro do portal que respondia as dúvidas de RH do colaborador em linguagem natural, citando o documento de origem. O widget ficou na interface do portal de RH de 21 de agosto a 24 de setembro de 2026, para quem tinha acesso, e foi desenhado para ficar longe de dado sensível. Tirei o widget numa rodada de auditoria; a página completa continuou respondendo até 9 de outubro, quando troquei por uma central de ajuda que dispensa LLM e desliguei o caminho do LLM. A última seção conta isso.

**Papel:** autor único, ponta a ponta (spec, arquitetura, o sidecar RAG em Python, o harness de avaliação e o deploy em produção).
**Stack:** Python · LangGraph · pgvector (HNSW) · embeddings locais (fastembed, multilingual-e5-large) · Claude Haiku · FastAPI · Postgres.

## O problema

O pessoal da empresa vivia perguntando as mesmas poucas coisas ao RH sobre benefícios e políticas, tudo enterrado em documentos que ninguém queria vasculhar, e as respostas saíam lentas e desencontradas. Eu queria um assistente que respondesse em linguagem simples e mostrasse de onde tirou a resposta, sem nunca indexar salário, avaliação de desempenho ou qualquer dado pessoal.

## A arquitetura

O desenho segue o padrão sidecar. O app de RH (Next.js) delega a recuperação a um serviço Python isolado, chamado por HTTP interno autenticado por token. A lógica de recuperação em si é uma máquina de estados em LangGraph:

```
retrieve → grade → route → generate | fallback
```

- **retrieve** embeda a pergunta localmente e busca os top-k trechos por similaridade de cosseno no pgvector.
- **grade** pergunta ao modelo quais trechos são de fato relevantes, mais uma confiança (structured output, JSON schema).
- **route** escolhe o passo seguinte. Confiança suficiente e trechos mantidos → generate; senão → fallback.
- **generate** responde só pelo contexto recuperado, citando o documento pelo nome. Sem citação fundamentada → vira fallback.
- **fallback** diz "Não encontrei essa informação." em vez de chutar.

Os embeddings rodam localmente (ONNX, e5-large, 1024 dims), então o texto dos documentos nunca sai da infra para ser vetorizado. Os vetores ficam no mesmo Postgres do app, atrás de um índice HNSW de cosseno criado por uma migration Prisma editada à mão (o operador `<=>` roda em SQL cru, porque o ORM não o expõe).

## Destaques de engenharia

- **Eval gold-standard como gate de release.** 16 perguntas reais de RH mais sondas de PII. O build falha se o hit-rate@5 cai abaixo do limiar, ou se qualquer sonda de PII vaza em vez de retornar fallback. As métricas são funções puras, testáveis isoladamente.
- **Guardrails alinhados ao OWASP LLM Top 10.** O contexto recuperado é delimitado e tratado como dado, não instrução (prompt injection). A ingestão remove caracteres invisíveis e bidi, o tipo de instrução escondida que humano nenhum vê. Tamanho da pergunta e do upload têm teto. Uma heurística de CPF/CNPJ avisa o admin na hora da ingestão.
- **Só citação fundamentada.** Uma citação só é aceita se o documento existe no corpus; resposta sem citação fundamentada é rebaixada a fallback automaticamente.
- **RBAC e LGPD por desenho.** Login obrigatório, ingestão restrita a RH/admin, só documentos institucionais não-sensíveis indexados, e só o trecho recuperado mais a pergunta chegam ao provedor de LLM.
- **Isolamento de dados provado por teste.** O sidecar só toca as duas tabelas do RAG. Um teste varre o SQL do serviço para provar que nenhuma tabela operacional é referenciada e que não há segredo hard-coded. Tudo vem de variável de ambiente.

## Resultados

Cerca de cinco semanas visível na interface para quem tinha acesso. No conjunto de eval (fictício), o hit-rate@5 é 1,0 nas 16 perguntas, um conjunto pequeno e feito à mão. Faço o gate por hit-rate em vez de precision de propósito. O corpus de eval tem poucos trechos por documento, então precision ficaria baixa e diria pouco ali. A latência "warm" fica em torno de 2,4–4s em CPU, com o cold start eliminado pelo pré-aquecimento do modelo no boot. Os embeddings não custam por token e os vetores ficam num banco que já existia.

## O que entrou no lugar

Com o assistente fora da interface, o pessoal continuava precisando achar resposta, e eu não queria uma chamada de modelo a cada pergunta. Então construí uma central de ajuda que responde só com busca. Ela começa como busca lexical em TypeScript dentro do próprio app (radical leve, tolerância a erro de digitação, BM25 por campo e um dicionário de sinônimos) sobre os artigos de ajuda do sistema e a base de políticas do RH. Depois funde esse resultado com embeddings locais do mesmo modelo multilíngue que o RAG usava, por reciprocal rank fusion. Nenhum LLM gera nada, e se o serviço de embeddings cai ou passa de 800 ms a busca volta a ser só lexical. Cada artigo segue a mesma visibilidade de módulo do menu, então cada pessoa só vê ajuda do que acessa.

Medi num conjunto cego de 144 perguntas, escrito antes de qualquer ajuste por um agente que não tinha lido os artigos nem o motor. Nas 114 perguntas que têm resposta, a certa ficou no top 3 em 104 (91,2%), contra 71,9% da busca só lexical; a pergunta coloquial foi de 33,3% para 80,0%. Das 30 perguntas sem resposta, 90% voltam sinalizadas como tal. Não rodei o RAG antigo nesse conjunto, então isto não é uma comparação direta. O caminho do LLM ficou atrás de uma flag desligada, o que também encerrou o custo de inferência. O código do RAG continua no repo; o [rag-docs](https://github.com/monkaS013/rag-docs) é a versão aberta do mesmo pipeline.

> Sanitizado para uso público: sem identificação da empresa, nomes reais, hosts internos ou credenciais. O corpus de eval é fictício por desenho.
