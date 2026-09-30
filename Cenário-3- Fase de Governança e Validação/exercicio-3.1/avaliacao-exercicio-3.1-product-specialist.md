# Avaliação do Exercício 3.1 — Revisão crítica das respostas do assistente

| Campo | Valor |
|---|---|
| **Papel** | Product Specialist |
| **Cenário** | 3 — Governança e Validação |
| **Participante** | Jaqueline Santos |
| **Referências usadas** | `avaliacao-product-specialist.md` (cenário 3) e `prompt-avaliacao.md` (cenário 3) |
| **Não disponíveis nesta avaliação** | `avaliacao-foundation.md` (cenário 3), o enunciado completo do exercício e os Anexos A e B. As citações não foram conferidas contra os originais. |
| **Versão avaliada** | `docs/reviews/2026-09-avaliacao-respostas-staging.md` |
| **Data** | 30/09/2026 |

---

## Resumo

É uma revisão crítica de nível sênior. Cada resposta foi quebrada em afirmações, e cada afirmação foi conferida contra o Anexo A. As duas armadilhas obrigatórias foram identificadas na análise própria. A comparação com o Claude é honesta e registra um viés da própria autora nas respostas 3 e 5 (ter aceitado o "supervisor" porque ela mesma tinha escrito essa regra).

As propostas de ajuste são concretas e estão ligadas às regras do AGENTS.md. O padrão "a confiança autodeclarada pelo modelo está invertida" é o melhor achado do documento.

Os pontos fracos:

- **Resposta 2 rebaixada** em relação ao gabarito, usando uma categoria da escala que não se aplica ao caso.
- **Rótulo da resposta 6** na análise própria não usa a taxonomia do exercício.
- **Uso do Claude sem evidência:** não aparecem o prompt nem o output original.

---

## Scores por dimensão

| Dimensão | Score | Justificativa |
|---|---|---|
| D1 — Domínio Conceitual | 3 | Aplica revisão crítica de verdade: afirmação por afirmação, com a fonte e a confiança avaliadas como parte da resposta. Liga os achados ao harness: structured output que rejeita `answered` sem `sources`, confiança calculada por código e HITL em três níveis compatível com a meta de 30 s. |
| D2 — Uso de Ferramentas | 2 | O Claude foi usado como segundo avaliador, com foco definido ("aderência literal ao Anexo A"). Mas o documento não traz o prompt enviado, o contexto fornecido ao Claude nem o output original. Não dá para saber se a seção 2 é o que o Claude produziu ou uma versão editada. |
| D3 — Qualidade do Entregável | 3 | Está completo e correto. Tem escala definida, as duas avaliações, comparação em dois níveis, padrões transversais, ajustes por camada (prompt, interface, pipeline e harness), HITL e pendências com dono. |
| D4 — Pensamento Crítico | 3 | As armadilhas #4 e #6 foram encontradas na análise própria. A autora registra o próprio viés (respostas 3 e 5) e defende uma divergência do gabarito com argumento contratual (resposta 2). Mostra que, na #6, o documento formal que o gabarito espera **não existe**, e que o comportamento correto é declarar isso e acionar o HITL. Também faz a ressalva de que 6 respostas são poucas para medir a taxa de 12%. |
| D5 — Aplicabilidade ao Projeto | 3 | Referencia os IDs dos guardrails do 2.2 (DEVE-01, NDEVE-09), do AGENTS.md do 2.3 (AG-DEVE-02, AG-DUVIDA-02, AG-COD-01, PEND-02), o `QueryResponseSchema`, os incidentes INC-1 e INC-2, as armadilhas 2 e 3 do Anexo B e a meta de 30 s. |

**Score do exercício: 2,8**

---

## Verificação de armadilhas

| Armadilha obrigatória | Avaliação correta | Identificada? | Observação |
|---|---|---|---|
| **#4 — Política de carga danificada** | Alucinação | ✅ **Sim**, na análise própria | O PS chamou de "alucinação", apontou as condições inventadas e a combinação "sem fonte com confiança alta", e mostrou que a resposta distorce o FAQ-38. |
| **#6 — Carga perigosa + frete expresso** | Fonte não confiável | ✅ **Sim, no diagnóstico**; ⚠️ rótulo impreciso | A justificativa do PS identifica o problema certo ("o FAQ não é validado por Compliance"). Mas o veredito próprio é ❌ "Incorreta", e não "fonte não confiável". A comparação reconhece que a classificação do Claude prevalece. Como o diagnóstico está certo, não se aplica o limite "D4 ≤ 2". |

---

## Checklist da skill (Exercício 3.1)

| Critério | Nota | Evidência |
|---|---|---|
| Avaliação própria ANTES do Claude | 3 | A análise própria é substantiva e encontra as duas armadilhas de forma independente. **Ressalva:** nada no documento prova a ordem (ver melhoria 3). |
| Respostas corretas bem avaliadas | 2 | As respostas #1, #3 e #5 foram reconhecidas como corretas. A #2 foi rebaixada para 🟠 "Parcialmente correta". O argumento é bom ("48h" × "48h úteis" num documento contratual), mas pela escala do próprio documento o caso é 🟡 "correta com ressalvas" (defeito de unidade). O 🟠 foi definido para "fonte que não sustenta a afirmação com o peso dado". |
| Classificação do tipo de erro | 3 | A seção 5 aplica a taxonomia do exercício corretamente: #4 alucinação, #6 fonte não confiável, #1, #2, #3 e #5 informação incompleta. |
| Propostas de ajuste concretas | 3 | Cada erro tem um ajuste por camada, ligado a uma regra existente. Exemplos: schema que rejeita resposta sem fonte (#4), rótulo automático e confiança forçada para baixa quando só há fonte informal (#6), validação do par número + unidade (#2). |
| Comparação com o Claude honesta | 3 | Separa concordância total, diferença só de rótulo e divergência real. Em cada caso diz quem prevalece e por quê, incluindo os casos em que o Claude estava certo e o PS errado. |

---

## Pontos fortes

1. **Confiança invertida.** A confiança foi alta justamente nas duas respostas com erro grave (#4 e #6) e baixa na única resposta em que o documento é categórico (#5). Daí vem a proposta de confiança calculada por código (PEND-A), que é a conclusão de harness mais importante da amostra.
2. **O viés de autoria foi reconhecido.** Nas respostas 3 e 5, a autora aceitou o "supervisor" porque essa regra foi escrita por ela no 2.3. Registrar isso, e concluir que "a correção é na regra, não no modelo", é exatamente para que serve um segundo avaliador.
3. **HITL pensado para a meta de 30 s.** O documento separa o bloqueio (HITL-01, para tema sensível sem fonte normativa) do aviso no ponto de uso (HITL-02) e da fila de revisão depois do fato (HITL-03). A resposta sensível é bloqueada de verdade, e não apenas registrada em log.

---

## Pontos de melhoria

1. **Alinhar o veredito da resposta 2 à própria escala.** Mantenha o argumento contratual e troque o rótulo para 🟡 "correta com ressalvas: risco contratual". Se a sua posição é que omitir a unidade num documento contratual é mais que ressalva, crie esse critério na escala ("🟠 também quando omitir a unidade muda um compromisso contratual") para que o rebaixamento siga uma regra.
2. **Rótulo da resposta 6 na análise própria:** use "fonte não confiável", que é o termo do exercício, e deixe o impacto no produto na justificativa. A comparação já chega a essa conclusão. Falta aplicá-la no veredito do PS.
3. **Registrar a evidência do uso do Claude e da ordem das análises:**
   - o prompt enviado ao Claude e o contexto que ele recebeu (Anexo A? os guardrails?);
   - o output original, sem edição;
   - uma linha dizendo que a avaliação do PS foi feita antes, sem ver o output do Claude (idealmente com data e hora).

   Isso sobe D2 e mostra que a análise foi "humano primeiro".
4. **Resolver uma tensão sobre a resposta 4.** O texto diz que "não existe documento formal sobre carga danificada" (lacuna 1). Mas a própria tabela aponta que a POL-001 §3.5 trata **avaria em trânsito** (devolução sem custo), o que você também concluiu nos exercícios 1.1 e 1.3. A resposta continua sendo alucinação, porque inventa uma política e condições que não existem. Só que a resposta correta esperada não é "não encontrei": é citar a POL-001 §3.5 e sinalizar a divergência com o FAQ-38. Escreva isso de forma explícita.
5. **Detalhe da comparação:** na resposta 3, a análise do PS já sugeria incluir o ramal 4500. O viés de autoria fica mais bem descrito como "aceitou o supervisor como correto, mas percebeu a falta do ramal", e não como uma omissão completa.

---

## Classificação

**Aprovado com distinção (2,8)**

## Tópicos da trilha para reforço

Não é obrigatório, porque o score está acima de 2,5. Como opcional, vale revisar em Revisão Crítica de Outputs de IA como calibrar o veredito de acordo com uma escala definida antes da análise, e como registrar a evidência do método "humano primeiro, IA depois".

---

> **Observação:** esta avaliação foi feita por IA e não substitui a validação do avaliador humano. Isso vale principalmente para confirmar que a análise do PS foi feita antes da análise do Claude e para as citações dos Anexos A e B.
