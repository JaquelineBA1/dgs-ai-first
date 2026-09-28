# Exercício 1.3 — Especificação de requisitos de RAG (ponto de vista de produto)

**Papel:** Product Specialist (Analista de Requisitos)
**Ferramenta utilizada:** Claude (chat)

## Contexto
Esta especificação traduz os riscos já identificados no discovery — duas versões contraditórias do PROC-042, um FAQ informal sem validação oficial, e ausência de metadado de vigência documental — em requisitos concretos para o pipeline de RAG (a camada que decide o que o assistente pode consultar e como ele cita o que encontrou). Cada requisito vem com um critério de teste que o QA pode executar sem precisar entender a arquitetura interna do sistema.

## Especificação — versão inicial (v1)

Cobrir os 5 pontos abaixo:

1. **Fontes a indexar (e o que excluir/marcar como obsoleto):**
   <!-- ... -->
2. **Tratamento de documentos contraditórios:**
   <!-- ex: como lidar com PROC-042 v1 vs v2 -->
3. **Comportamento quando não há resposta na base:**
   <!-- dizer "não encontrei" vs. tentar responder com conhecimento geral -->
4. **Requisitos de atualização:**
   <!-- prazo máximo entre publicação de um documento novo e disponibilidade no assistente -->
5. **Requisitos de rastreabilidade:**
   <!-- toda resposta deve citar fonte? mostrar o trecho relevante? -->

## Iteração com o Claude

**Prompt para revisão da v1:**
```
<!-- Preciso de uma especificação de requisitos de produto (não técnica, mas
precisa) para o pipeline de RAG do assistente de atendimento da NovaTech.
Cubra exatamente estes 5 pontos, usando os achados reais do nosso caso como
referência:

1. Quais fontes de dados devem ser indexadas, e o que deve ser excluído ou
   marcado como obsoleto.
2. Como o assistente deve lidar com documentos contraditórios (ex: duas
   versões de um mesmo procedimento, como o PROC-042 v1 e v2).
3. Qual o comportamento esperado quando a pergunta do atendente não tem
   resposta na base.
4. Requisitos de atualização — em quanto tempo um documento novo publicado
   deve estar disponível no assistente.
5. Requisitos de rastreabilidade — se toda resposta deve citar a fonte e
   mostrar o trecho relevante.

Cada requisito precisa ser testável, para que o QA consiga verificar se foi
atendido. -->
```

**Feedback recebido do Claude:**
```
<!-- # Especificação de Requisitos — Pipeline de RAG do Assistente de Atendimento NovaTech

**Autor:** Jaqueline Santos, Product Specialist
**Base:** achados da fase de discovery (mapa de temas, análise de inconsistências PROC-042 v1/v2, cruzamento com o FAQ-Atendimento, mapa de riscos)
**Público:** não técnico, mas com critérios verificáveis por QA

## Contexto

Esta especificação traduz os riscos já identificados no discovery — duas versões contraditórias do PROC-042, um FAQ informal sem validação oficial, e ausência de metadado de vigência documental — em requisitos concretos para o pipeline de RAG (a camada que decide o que o assistente pode consultar e como ele cita o que encontrou). Cada requisito vem com um critério de teste que o QA pode executar sem precisar entender a arquitetura interna do sistema.

---

## 1. Fontes de dados: o que indexar, excluir ou marcar como obsoleto

| # | Requisito | Critério de teste (QA) |
|---|-----------|--------------------------|
| RF1.1 | Só é indexado documento com responsável formal identificado e tipo classificado (Política, Procedimento, Tabela de SLA). | Amostrar a base indexada e confirmar que 100% dos itens têm os campos "responsável" e "tipo de documento" preenchidos; qualquer documento sem esses campos deve ter sido rejeitado na ingestão. |
| RF1.2 | Documento sem controle de versão formal (caso do FAQ-Atendimento) pode ser indexado, mas só com uma marca explícita de "fonte não validada / informal", nunca com o mesmo peso de um documento normativo. | Perguntar algo cuja única fonte disponível é o FAQ (ex.: existência do tier "Platinum") e confirmar que a resposta inclui o aviso de fonte não validada antes de qualquer conteúdo extraído dele. |
| RF1.3 | Quando duas versões do mesmo documento coexistem sem data de obsolescência formalmente confirmada (caso do PROC-042 v1/v2), as duas permanecem indexadas, mas o sistema marca a vigência como "indefinida — requer confirmação humana" em vez de escolher uma sozinho. | Consultar o par PROC-042 v1/v2 na base e confirmar que o atributo de vigência retorna "indefinida", não uma das duas datas isoladamente. |
| RF1.4 | Documento citado como estando em revisão por outra área (ex.: PROC-043, mencionado no PROC-042 v2 como "em revisão pelo Compliance") recebe a marca "sujeito a alteração" até confirmação da área responsável. | Verificar que qualquer resposta que cite PROC-043 venha acompanhada do aviso de revisão em andamento. |
| RF1.5 | Nenhum documento é excluído da base só por estar desatualizado; a exclusão exige confirmação explícita e registrada do responsável formal (Operações, Comercial ou Compliance). | Tentar remover um documento sem essa confirmação registrada e confirmar que o pipeline rejeita a operação ou a coloca como pendente de aprovação. |

---

## 2. Comportamento diante de documentos contraditórios

| # | Requisito | Critério de teste (QA) |
|---|-----------|--------------------------|
| RF2.1 | Quando a pergunta cair numa área com informação numérica/procedimental conflitante entre fontes (ex.: multiplicador de frete divergente entre PROC-042 v1 e v2), o assistente nunca devolve um único valor misturando elementos das duas fontes. | Perguntar "qual o multiplicador de frete para a região Sul?" e confirmar que a resposta apresenta explicitamente os dois valores (1,2 e 1,3) com suas respectivas fontes — nunca um valor único ou uma combinação. |
| RF2.2 | Toda resposta que envolva fontes conflitantes inclui um aviso padronizado de "fontes conflitantes — confirmação humana recomendada", com sugestão de canal de escalonamento. | Verificar a presença literal desse aviso em qualquer resposta que cite mais de uma versão do mesmo procedimento. |
| RF2.3 | O assistente não aplica "usar sempre a versão mais recente" como regra padrão de desambiguação, a menos que o campo de vigência (RF1.3) indique isso claramente. | Testar um cenário em que a versão mais nova existe mas o campo de vigência está "indefinido", e confirmar que o assistente não a escolhe automaticamente — mostra as duas. |
| RF2.4 | Uma regra vinda de fonte informal (ex.: critério de "data do contrato" citado no FAQ) nunca se sobrepõe à regra documentada formalmente (cláusula de transição por data de chamado, PROC-042 v2 §5); se as duas aparecerem, a formal prevalece na citação principal. | Perguntar sobre a transição de tabela de frete e confirmar que a resposta cita a cláusula formal (§5) como regra primária, com o critério do FAQ aparecendo só como nota secundária rotulada como não validada. |

---

## 3. Comportamento quando a pergunta não tem resposta na base

| # | Requisito | Critério de teste (QA) |
|---|-----------|--------------------------|
| RF3.1 | Quando a base não tiver nenhuma fonte relevante, o assistente declara explicitamente que não encontrou informação — nunca gera uma resposta aproximada a partir de conhecimento geral. | Perguntar sobre um tema comprovadamente ausente da base (ex.: processo de sinistro por carga danificada, hoje só descrito informalmente no FAQ e sem política formal) e confirmar que a resposta usa a frase padrão de "não encontrado", sem conteúdo inventado. |
| RF3.2 | Nesse caso, o assistente oferece a ação de escalonar para o supervisor com um resumo automático da pergunta original. | Confirmar que, ao retornar "não encontrado", a interface oferece uma ação de escalonamento pré-preenchida com o resumo da dúvida. |
| RF3.3 | Se a única fonte disponível for informal (FAQ), o assistente apresenta essa fonte claramente rotulada como tal — nunca trata como "não encontrado" nem como resposta de aparência oficial. | Perguntar algo cuja única fonte é o FAQ (ex.: critério do tier "Platinum") e confirmar que a resposta rotula a fonte como informal, em vez de "não encontrado" ou apresentação oficial. |

---

## 4. Requisitos de atualização (SLA de ingestão)

| # | Requisito | Critério de teste (QA) |
|---|-----------|--------------------------|
| RF4.1 | Um documento novo publicado por uma área responsável fica disponível para consulta do assistente em, no máximo, **[X horas úteis — valor a validar com Operações/TI; proponho 24h úteis como ponto de partida]** após a publicação. | Publicar um documento de teste e medir o tempo até ele aparecer como fonte citável nas respostas — falha se ultrapassar o prazo definido. |
| RF4.2 | Quando um documento existente é substituído por uma nova versão com vigência explícita, a versão antiga deixa de ser citada como fonte primária dentro do mesmo prazo do RF4.1 (permanece disponível só como histórico consultável). | Publicar uma nova versão de um documento de teste já indexado e confirmar que, após o prazo, o assistente cita só a nova versão como vigente. |
| RF4.3 | O prazo de atualização é o mesmo independentemente da diretoria responsável pelo documento (Comercial, Operações ou conjunta) — não existe SLA de ingestão diferente por área. | Publicar documentos de teste atribuídos a diretorias diferentes e confirmar que o tempo de disponibilização é idêntico em todos os casos. |

*Nota: o valor de X no RF4.1 é uma proposta de baseline, não um número validado — precisa ser confirmado com quem for operar o pipeline antes de virar meta oficial de SLA.*

---

## 5. Requisitos de rastreabilidade

| # | Requisito | Critério de teste (QA) |
|---|-----------|--------------------------|
| RF5.1 | Toda resposta baseada em conteúdo de um documento cita o nome do documento, a versão e a seção/trecho exato de onde a informação foi extraída. | Amostrar perguntas cobrindo os 4 temas do discovery (prazo, frete, devolução, outros) e confirmar que 100% das respostas trazem nome do documento + versão + referência de seção. |
| RF5.2 | O trecho-fonte relevante é exibido ao atendente diretamente na resposta, não apenas referenciado por nome ou link. | Confirmar visualmente que a interface mostra o trecho-fonte (ou um resumo fiel a ele) junto com cada resposta. |
| RF5.3 | Quando a resposta vem de mais de uma fonte (cenário de contradição, seção 2), todas as fontes usadas são citadas individualmente — nunca mescladas numa citação genérica. | No cenário de contradição do PROC-042 v1/v2, confirmar que a resposta lista as duas citações separadamente, cada uma com sua versão. |
| RF5.4 | Nenhuma resposta é apresentada sem fonte identificável; se o assistente não conseguir associar a resposta a uma fonte específica, trata isso como "não encontrado" (seção 3). | Auditar uma amostra de respostas e confirmar ausência de qualquer resposta sem citação de fonte. |

---

## Pendências para validar antes do desenvolvimento

O valor do prazo de ingestão (RF4.1/RF4.2) ainda não tem número confirmado pela NovaTech — está proposto como baseline de 24h úteis. A definição de quem tem autoridade formal para marcar um documento como obsoleto (RF1.5) também depende de uma decisão de governança que ainda não foi validada com Operações/Comercial/Compliance. -->
```

## Especificação — versão refinada (v2)

<!-- versão final, incorporando o feedback -->

## Entregável

- [ ] Especificação final (v2)
- [ ] Histórico de iteração (v1 → feedback do Claude → v2)

