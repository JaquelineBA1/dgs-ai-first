# Avaliação do Exercício 2.3 — Seção "Product Rules & Guardrails" do AGENTS.md

| Campo | Valor |
|---|---|
| **Papel** | Product Specialist |
| **Cenário** | 2 — Estruturação do Trabalho |
| **Participante** | Jaqueline Santos |
| **Referências usadas** | `avaliacao-product-specialist.md` (cenário 2) e `prompt-avaliacao.md` (cenário 2) |
| **Não disponíveis nesta avaliação** | `avaliacao-foundation.md` (cenário 2) e Anexos A e C. Não foi possível conferir as citações nem os paths do repositório contra os originais. |
| **Versão avaliada** | Seção `product-rules-and-guardrails` v1.0 |
| **Data** | 30/09/2026 |

---

## Resumo

A seção está pronta para um agente usar. Ela tem:

- **Metadados:** bloco de metadados em comentário HTML e uma instrução direta ao agente.
- **Regras:** IDs estáveis com precedência explícita, e cada regra indica como é garantida (código ou prompt) e em qual arquivo.
- **Glossário:** coluna "NÃO confundir com" e o identificador que o termo tem no código.
- **Contrato:** um contrato em Zod que transforma guardrails em validação.
- **Restrições de código:** 12 restrições que o Copilot consegue seguir, como "proibido criar `calculateFreight`" e "teste nomeado com o ID da regra".

Os 9 guardrails simulados aparecem na seção. As adaptações estão justificadas e as divergências ficaram registradas em pendências.

A nota não chega a 3 por dois motivos. O primeiro é que um gatilho do validador está mal delimitado e há uma checagem numérica que gera falso positivo. O segundo é que não há evidência do uso do Claude nem de teste com o Copilot.

---

## Scores por dimensão

| Dimensão | Score | Justificativa |
|---|---|---|
| D1 — Domínio Conceitual | 3 | Trata o AGENTS.md como artefato prescritivo, e não como documentação. Estabelece que uma regra 🔒 "nunca pode depender só de instrução no prompt", transforma guardrails em schema (`superRefine` com o ID da regra na mensagem de erro) e liga cada regra a testes (AG-COD-11) e golden queries (AG-COD-12). |
| D2 — Uso de Ferramentas | 2 | Não há prompt, output nem histórico do uso do Claude. Também não há um teste com o Copilot que mostre que as restrições mudam o código gerado. O enunciado não exige esse teste, mas o critério "concretas o suficiente para influenciar o output do Copilot" fica sem evidência. |
| D3 — Qualidade do Entregável | 3 | A seção está completa (as 4 partes pedidas mais pendências) e é prescritiva e parsável. O que falta ajustar está numa cláusula específica (AG-COD-03) e no schema, e a correção é rápida (ver "Problemas encontrados"). |
| D4 — Pensamento Crítico | 3 | Segue o guardrail simulado "priorizar a mais recente", mesmo contrariando a decisão do 2.2, e registra a tensão com a spec de RAG e com o cabeçalho da PROC-042-v2 (PEND-01). Nota que "supervisor" não existe no Anexo A (PEND-02) e que `domain-terms.ts` não está no Anexo C (PEND-04). Troca "vigente" por "mais recente, com a data de emissão". Incorporou parte do feedback do 2.2: tirou "substitui" da lista de bloqueio, transformou as listas em constantes e passou a nomear os testes pelo ID. |
| D5 — Aplicabilidade ao Projeto | 3 | Usa os paths do Anexo C, as ADR-0001 a 0004 (incluindo 5 chunks e 3 turnos), os specs dos 5 módulos, os metadados do pipeline de ingestão e o limite de 30 s. |

**Score do exercício: 2,8**

---

## Checklist da skill (Exercício 2.3)

| Critério | Nota | Evidência |
|---|---|---|
| Machine-readable | 3 | Metadados em comentário HTML, IDs estáveis, tabelas com colunas fixas, schema em código, paths do repositório e constantes nomeadas (`CLIENT_TIERS`, `DANGEROUS_CARGO_TERMS`). |
| Glossário conectado ao recorte de domínio | 3 | Consistente com o glossário do 2.1, com link para o documento completo. Acrescenta o identificador no código (`ticketType: "critical"`, `isSpecialFreight(weightKg) > 500`). |
| Restrições de código concretas | 3 | Exemplos: AG-COD-02 (a citação é montada a partir dos metadados, o LLM devolve só `used_chunk_ids`), AG-COD-05 (proibido criar função de cálculo de frete), AG-COD-06 (a versão é escolhida só por `document_date`, nunca pelo sufixo `-v2`) e AG-COD-07 (mensagens fixas como constantes). |
| Consistente com os guardrails fornecidos | 3 | Os 3 DEVE, os 3 NÃO DEVE e os 3 QUANDO EM DÚVIDA estão refletidos. A escalação foi adaptada para "área do documento, e o supervisor quando não houver área", com justificativa e registro em PEND-02. |

---

## Verificação de artefatos machine-readable

**Prescritivo, e um agente consegue seguir:**

- "Todo retorno de `handler.ts` **DEVE** passar por `QueryResponseSchema`" (AG-COD-01).
- "Proibido criar função que calcule valor de frete (ex.: `calculateFreight`, `valorBase * multiplicador * fator`)" (AG-COD-05).
- "`it("AG-NDEVE-02: …")`": o formato de nome de teste está definido (AG-COD-11).
- "A escolha entre versões usa **só** `document_date` e `document_family`. Nunca usa nome de arquivo, ordem de recuperação ou sufixo `-v2`" (AG-COD-06).

**Checklist de prescritividade: itens narrativos ou vagos, com a reescrita sugerida**

| Trecho | Classificação | Reescrita sugerida |
|---|---|---|
| AG-DEVE-03 "norma culta, tratamento impessoal" | Vago | "NÃO DEVE usar: 'a gente', 'pra', 'tá', emojis, gírias (lista em `domain-terms.ts`). DEVE usar a 3ª pessoa ou a voz impessoal." |
| Pendências: "os agentes não devem implementar comportamento para estes pontos além do que está descrito" | Narrativo | "Ao encontrar um ponto pendente, NÃO implemente uma decisão. Insira `// TODO(PEND-0n)` e mantenha o comportamento mais restritivo (ex.: PEND-03 → devolver `not_found`)." |
| "Antes de gerar código… leia esta seção inteira" | Aceitável | Mantenha, mas acrescente quais IDs são obrigatórios para cada pasta (ex.: `src/services/response-validator.ts` → AG-COD-03 e AG-NDEVE-01 a 04). |

---

## Problemas encontrados

1. **O gatilho de carga perigosa no AG-COD-03(b) está mal delimitado.** O AG-DEVE-04 vale para perguntas sobre **devolução** de carga perigosa, mas o AG-COD-03(b) reprova qualquer resposta em que o gatilho dispare sem "processo padrão" e "4500". Duas respostas corretas seriam reprovadas:
   - "Frete de carga perigosa de 800 kg": a resposta certa cita a PROC-043 como fora da base.
   - "Carga perigosa com documentação irregular, cliente Gold": a resposta certa é incidente crítico, SLA-2024 §3.

   Além disso, com o texto sem acentos e comparação por trecho, os termos `gás`/`gases` casam com "gastos". **Correção:** o gatilho passa a exigir um termo de carga perigosa **e** um termo de devolução, e a comparação passa a ser por palavra inteira.

2. **AG-COD-03(b) só verifica se os termos aparecem.** Uma resposta como "pode devolver pelo processo padrão, ligue para o ramal 4500" contém os dois termos e passaria. Isso já tinha sido apontado no 2.2. A classificação "🔒 código + 💬 prompt" do AG-NDEVE-02 está correta, mas falta dizer qual parte é garantida pelo código e qual depende de golden query (VC-01, VC-23).

3. **A checagem numérica do AG-NDEVE-01 gera falso positivo e deixa passar o erro mais grave.**
   - **Falso positivo:** "todo token numérico da resposta tem que existir num chunk" pega números de citação (`§3.2`, o "2024" de `SLA-2024`, `Item 15`) e de textos fixos. Também reprova reformulações corretas: a POL-001 §3.1 diz "7 **(sete)** dias úteis", e a resposta "7 dias úteis" não bate como par número + unidade (AG-DEVE-05). **Correção:** excluir da checagem os tokens de citação e metadado e normalizar numerais por extenso.
   - **Erro que passa:** o valor 1.8 existe no chunk da v2, então citá-lo como se fosse da v1 passaria. Falta um item **(e)** no AG-COD-03: "um valor atribuído a uma versão tem que existir no chunk **dessa versão**". Essa é a proteção contra o INC-2 e contra misturar versões quando a regra é priorizar a mais recente (AG-DUVIDA-03).

4. **O schema deixa três decisões em aberto.**
   - **Campo principal:** com várias fontes, não está definido qual vai no `source_document` de nível superior. Sugestão: exigir `source_document === sources[0].source_document`.
   - **Escalação no not_found:** o guardrail simulado manda sugerir escalação, então falta `status = "not_found"` implicar `escalation_suggested = true`.
   - **Dependência nova:** o `zod` não aparece como dependência no Anexo C. Registre isso como pendência para o TL, para o agente não instalar o pacote por conta própria.

5. **Valores de negócio dentro do AGENTS.md.** O glossário traz todos os multiplicadores e fatores de peso das duas versões, enquanto o AG-COD-04 proíbe esses valores em `src/`. Com os números no contexto, o Copilot pode sugeri-los no código. E como as três áreas atualizam os documentos todo mês, a cópia no AGENTS.md vai ficar desatualizada. **Sugestão:** no glossário, ficar só com a definição e a seção ("fator por região; valores em PROC-042 §2.1 e PROC-042-v2 §2.1") e deixar os números em `tests/fixtures/`.

6. **A seção ficou longa para um AGENTS.md.** Agentes carregam o AGENTS.md inteiro no contexto. Uma opção é deixar aqui as regras, o schema e as restrições de código, e mover o glossário detalhado para `docs/domain/`, que já tem link.

---

## Pontos fortes

1. **Guardrail vira contrato.** O `QueryResponseSchema` com `superRefine` faz com que AG-DEVE-02, AG-DUVIDA-01 e AG-DUVIDA-04 sejam validados em toda resposta, e a mensagem de erro já traz o ID da regra.
2. **As restrições bloqueiam os atalhos que um agente tomaria.** Proibir `calculateFreight`, a escolha de versão pelo sufixo `-v2` e texto de aviso gerado pelo LLM corta exatamente o que um Copilot faria sem contexto.
3. **Divergências registradas, e não escondidas.** PEND-01 a PEND-04 têm dono e mostram as regras afetadas. O guardrail simulado foi seguido sem apagar o conflito com as decisões anteriores.

---

## O que fazer antes de entregar (maior impacto com menos esforço primeiro)

1. **Corrigir o gatilho do AG-COD-03(b)** para exigir carga perigosa **e** devolução, com comparação por palavra inteira (problema 1).
2. **Acrescentar o item (e) ao AG-COD-03** (valor precisa estar no chunk da versão citada) e excluir os tokens de citação da checagem numérica (problema 3).
3. **Completar o schema:** fonte principal igual a `sources[0]`, `not_found` com escalação e zod registrado como pendência para o TL (problema 4).
4. **Tirar os valores numéricos do glossário** e apontar para as seções dos documentos (problema 5).
5. **Registrar a evidência de uso das ferramentas:**
   - o prompt enviado ao Claude e o que você ajustou no output;
   - um teste rápido com o Copilot, por exemplo pedir "crie o handler do query endpoint" com a seção carregada, e anotar o que ele seguiu (schema, `used_chunk_ids`) e o que ignorou.

   Isso sobe D2 e é a melhor prova do critério "influencia o Copilot".
6. **Reescrever os trechos narrativos** do checklist de prescritividade, principalmente a regra de `TODO(PEND-0n)`.

Com os itens 1 a 5 feitos, a nota provavelmente fica entre 2,8 e 3,0, com D2 subindo para 3.

---

## Classificação

**Aprovado com distinção (2,8)**

## Tópicos da trilha para reforço

Não é obrigatório, porque o score está acima de 2,5. Como opcional: **AGENTS.md**, sobre o tamanho da seção no orçamento de contexto do agente e o risco de o Copilot sugerir valores de negócio que estão escritos no próprio AGENTS.md. E também como testar se o Copilot segue as instruções do AGENTS.md.

---

> **Observação:** esta avaliação foi feita por IA e não substitui a validação do avaliador humano. Isso vale principalmente para os paths do Anexo C e as citações do Anexo A, que não pude conferir.
