# Avaliação do Exercício 1.1 — Mapeamento de intent com engenharia de contexto (3ª rodada)

**Papel:** Product Specialist · **Cenário:** 1 — Entendimento e Contexto

## O que mudou desde a última versão

As alterações foram só de estrutura: você colocou o texto do enunciado de cada etapa no documento (a Etapa 1 foi para a seção Contexto; as Etapas 2 e 3 foram para os títulos). Prompts, outputs, análises críticas, mapa de riscos e reflexão estão idênticos à versão anterior.

Com o enunciado no documento, pude conferir se cada etapa fez o que foi pedido, e isso muda um ponto da avaliação anterior:

- **Etapa 1:** está conforme. O enunciado pede "apenas títulos, metadados e resumos". Isso reforça a sua própria autocrítica, porque datas e status de vigência são metadados e podiam ter entrado.
- **Etapa 2:** está conforme. O enunciado pede 2 documentos escolhidos a partir do mapa, só com o conteúdo completo desses dois.
- **Etapa 3:** está conforme. O enunciado manda fornecer "o output das etapas 1 e 2" junto com o FAQ. Retiro a crítica de que colar os outputs inteiros foi um erro seu. Você seguiu a instrução. A observação da sua reflexão sobre curar o estado entre etapas continua válida, mas agora como melhoria que vai além do enunciado, e não como falha.

## Scores por dimensão

| Dimensão | Score | Justificativa |
|---|:---:|---|
| D1 — Domínio Conceitual | 3 | Sem mudança. A reflexão continua sendo o ponto alto: progressive disclosure como método de verificação, propagação de erros, ancoragem, e a lição aplicada ao RAG. |
| D2 — Uso de Ferramentas | 2 | Sem mudança. Os prompts continuam sem formato de saída. O output da Etapa 3 ainda tem seções que o prompt registrado não pede (as seções 5 e 6). A verificação com o Anexo A continua sem prompt nem output. |
| D3 — Qualidade do Entregável | 3 | Está completo e utilizável. Porém a inclusão do enunciado deixou a estrutura menos consistente (detalhes abaixo). |
| D4 — Pensamento Crítico | 3 | Sem mudança. Você identifica 7 erros do Claude, 2 próprios e ambiguidades inéditas na v2. |
| D5 — Aplicabilidade ao Projeto | 3 | Sem mudança. |

**Score do exercício: 2,8**

## Checklist da skill

| Critério | Nota |
|---|:---:|
| Estratégia de 3 etapas coerente | 3 |
| Escolha da etapa 2 justificada | 3 |
| Reflexão progressive disclosure | 3 |
| Riscos identificados | 3 |
| Prompts específicos | 2 |

## Verificação de armadilhas

Nada mudou em relação à rodada anterior:

- 5 documentos completos no primeiro prompt: **evitada**.
- PROC-042 v1 e v2 coexistindo sem hierarquia: **identificada**.
- Regra de transição do §5, lida a partir de 2026: **identificada**.
- Item 45 do FAQ, regra híbrida entre v1 e v2: **identificada**.
- Item 8 do FAQ ("multiplicadores mais altos" e critério por contrato): **identificada**.
- Tier Platinum (item 15): **identificada**.
- Item 38 contra a POL-001 §3.5: **identificada**, via Anexo A.
- Item 27 contra a definição de incidente crítico do SLA: **identificada**, via Anexo A.
- Item 41 afirmando além do que os resumos diziam: **identificada**.

## Problemas novos de estrutura

- **Títulos inconsistentes.** A Etapa 1 manteve o título `## Etapa 1 — Visão geral (metadados apenas)` e teve o enunciado posto em Contexto. As Etapas 2 e 3 viraram títulos com o enunciado em negrito (`## Etapa 2 — Análise profunda:`). Padronize: título curto e, logo abaixo, uma linha "Enunciado: …" em cada etapa.
- **Contexto ainda com cara de template.** Continua o "você vai pré-analisar…", agora com o enunciado da Etapa 1 grudado sem quebra de linha. Reescreva em primeira pessoa, em 2 ou 3 frases, ou transforme num bloco de citação do enunciado, separado do seu resumo.

## Pontos fortes

Continuam os mesmos: você audita a IA de forma rigorosa, a reflexão mostra humildade de método (inclusive admite que não rodou um grupo de controle), e o mapa de riscos tem prioridade e cita a seção de origem.

## O que ainda falta para chegar a 3,0

Nenhum dos três itens de maior impacto da rodada anterior foi feito:

1. **Registrar o prompt real da Etapa 3**, o que gerou as seções 5 e 6, e incluir formato de saída nos prompts. É o único ponto que segura D2 em 2.
2. **Documentar a verificação com o Anexo A.** Um prompt curto com o output basta. Ela sustenta os Riscos 5, 6 e 7 e a principal lição da reflexão.
3. **Usar os termos da trilha** ("orçamento de atenção" e "context rot") na reflexão, já que a skill avalia D1 com rigor.

Continuam também os ajustes de acabamento: o título do Mapa de Riscos está duplicado, os tópicos soltos depois da tabela parecem notas de revisão, e "incremental.." tem um ponto a mais.

## Classificação

**Aprovado com distinção (2,8).** A nota se mantém porque o conteúdo avaliado não mudou. Trazer o enunciado ajudou a avaliação a ser mais precisa, e foi por isso que retirei a crítica sobre a Etapa 3.

## Tópicos da trilha para reforço

Não é obrigatório, porque o score está acima de 2,5. Como opcional, vale revisar **Engenharia de Prompt: definir formato de saída e restrições**.

---

> ⚠️ A avaliação por IA não substitui a validação do avaliador humano, principalmente quanto às citações do Anexo A, que eu não consegui conferir.
