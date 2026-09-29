# Exercício 1.3 — Especificação de requisitos de RAG (ponto de vista de produto)

**Papel:** Product Specialist (Analista de Requisitos)
**Ferramenta utilizada:** Claude (chat)

## Contexto
Esta especificação traduz os riscos já identificados no discovery — duas versões contraditórias do PROC-042, um FAQ informal sem validação oficial, e ausência de metadado de vigência documental — em requisitos concretos para o pipeline de RAG (a camada que decide o que o assistente pode consultar e como ele cita o que encontrou). Cada requisito vem com um critério de teste que o QA pode executar sem precisar entender a arquitetura interna do sistema.

## Especificação — versão inicial (v1)

Cobrir os 5 pontos abaixo:

1. **Fontes a indexar (e o que excluir/marcar como obsoleto):**
A regra não é "excluir obsoletos", porque hoje nenhum documento declara se está obsoleto. A base indexa apenas documentos com metadados completos, e o status de vigência é decidido pelo dono, nunca inferido pela data.
Metadados obrigatórios por documento. Todo documento indexado deve ter: ID, título, versão, data de emissão ou atualização, status, tipo de fonte, área dona e relação com outras versões.
• Status possíveis: vigente, vigência não confirmada, em revisão, obsoleto.
• Tipo de fonte: normativo, contratual ou informal, conforme a "Classificação" no cabeçalho do documento.
Nada entra ou some em silêncio. Documento com extração de texto falha (ex.: PDF escaneado) ou sem metadados não é indexado e aparece no relatório de ingestão.

2. **Tratamento de documentos contraditórios:**
O assistente aplica a regra de transição quando o documento a define, e mostra as duas versões quando não define. "Usar sempre a mais recente" está descartado: contradiz a PROC-042-v2, seção 5.
Detectar o conflito. Quando os trechos recuperados vêm de versões diferentes do mesmo documento, ou de fontes que dizem coisas diferentes sobre o mesmo item, o assistente deve entrar em "modo conflito" e avisar o atendente.
• Aceite: para "Qual o multiplicador para o Sudeste?", a resposta sinaliza que a v1 diz 1.0 e a v2 diz 1.1.

Nunca misturar versões num cálculo. Multiplicador, fator de peso e prazo adicional de uma mesma resposta vêm da mesma versão.
• Aceite: para 2.000 kg ao Sudeste em chamado novo, nenhuma resposta combina 1.1 (v2) com 1.2 (v1). Teste da armadilha 1 do Anexo B.
Documento oficial prevalece sobre o FAQ. Havendo divergência entre um documento normativo ou contratual e o FAQ, vale o oficial, e a divergência é mostrada.
• Casos: FAQ-45 ("desconto automático acima de 10 fretes") × PROC-042-v2, seção 4 (5% a partir de 8). FAQ-38 (registro em 48h, Jurídico) × POL-001, seção 3.5 (avaria em trânsito é devolução sem custo).
• Aceite: nos dois casos, a resposta abre com a regra oficial e cita o FAQ apenas como divergência.
CON-06. Todo conflito vira registro. Cada conflito detectado é registrado com as fontes envolvidas e enviado à curadoria, para a área dona decidir.
• Aceite: após rodar o conjunto de testes da seção 7, o registro contém os conflitos PROC-042 × v2 e FAQ-45 × v2.
A hierarquia entre documento normativo e contratual, se os dois se contradisserem, ainda não está definida (pergunta em aberto).

3. **Comportamento quando não há resposta na base:**
   Quando a base não responde, o assistente diz "não encontrei", explica o que achou de mais próximo e por que não serve, e sugere escalar ao supervisor. Ele nunca completa a resposta com conhecimento geral. O mesmo vale para três casos que parecem ter resposta, mas não têm: cobertura parcial, documento fora da base e premissa falsa.
   Quando a base não responde, o assistente diz "não encontrei", explica o que achou de mais próximo e por que não serve, e sugere escalar ao supervisor. Ele nunca completa a resposta com conhecimento geral. O mesmo vale para três casos que parecem ter resposta, mas não têm: cobertura parcial, documento fora da base e premissa falsa.
SR-01. Limite do conhecimento geral. O modelo pode usar conhecimento geral para entender a pergunta e redigir a resposta. Ele não pode usá-lo para regras, prazos, valores, percentuais, contatos ou procedimentos da NovaTech. Toda inferência que não está num trecho deve ser declarada.
• Caso: "Manaus" → região Norte não está em nenhum documento. A resposta diz "considerando Manaus na região Norte" para o atendente confirmar.
• Aceite: em todo o conjunto de testes, nenhum prazo, valor ou contato aparece sem estar num trecho citado (verificado por RA-03).
SR-02. Resposta padrão de "não encontrei". Deve conter: (a) a frase explícita de que não encontrou; (b) o documento mais próximo, se houver, e por que não se aplica; (c) a sugestão de escalar ao supervisor (guardrail 3).
• Aceite: para "Frete para 300 kg para Salvador?", a resposta tem os três elementos e nenhum valor.
SR-03. Cobertura parcial conta como "não encontrado". Um trecho parecido, mas fora do escopo que o próprio documento declara, não autoriza resposta.
• Caso: a PROC-042-v2 vale para cargas acima de 500 kg (seção 1). O trecho PROC-042v2-B aparece para 300 kg (mapa de cobertura do Anexo B), mas não se aplica.
• Aceite: nenhum multiplicador da PROC-042 aparece em resposta sobre carga até 500 kg. Teste da armadilha 5 do Anexo B.
SR-04. Documento citado, mas fora da base. Quando um trecho remete a outro documento que não está indexado, o assistente informa o nome do documento e que ele não está disponível.
• Casos: carga perigosa acima de 500 kg → PROC-043 (PROC-042-v2, seção 4). Mercadoria em trânsito → PROC-088 (POL-001, seção 2).
• Aceite: para frete de carga perigosa de 800 kg, a resposta não aplica os multiplicadores gerais e cita a PROC-043 como fora da base.
SR-05. Premissa falsa é corrigida, não respondida. Se a pergunta traz algo que a base desmente, o assistente corrige a premissa com a fonte.
• Caso: "Qual o SLA do cliente Platinum?" → a SLA-2024, seção 1, diz que só existem Gold, Silver e Standard.
• Aceite: nenhuma resposta atribui SLA a um tier inexistente. Teste da armadilha 3 do Anexo B.

5. **Requisitos de atualização:**
 A atualização é automática, tem prazo contado a partir da publicação na fonte e vale também para retiradas. Operações, Compliance e Comercial publicam todo mês, e o Confluence muda toda semana: sem prazo, o assistente responde com a versão anterior sem ninguém perceber.

7. **Requisitos de rastreabilidade:**
  Toda resposta cita a fonte e mostra o trecho literal, e todo número da resposta tem de estar no trecho citado. Isso permite ao atendente conferir em segundos (etapa P5 da jornada) e ao QA verificar de forma automática.
Citação completa em toda resposta com regra. Toda resposta que traz regra, prazo, valor ou contato cita: documento, versão, data, seção e tipo de fonte.

## Iteração com o Claude

**Prompt para revisão da v1:**
```
Claude, revise esta versão do documento de requisitos V1.

## Especificação — versão inicial (v1)

Cobrir os 5 pontos abaixo:

**Fontes a indexar (e o que excluir/marcar como obsoleto):**
A regra não é "excluir obsoletos", porque hoje nenhum documento declara se está obsoleto. A base indexa apenas documentos com metadados completos, e o status de vigência é decidido pelo dono, nunca inferido pela data.
Metadados obrigatórios por documento. Todo documento indexado deve ter: ID, título, versão, data de emissão ou atualização, status, tipo de fonte, área dona e relação com outras versões.
• Status possíveis: vigente, vigência não confirmada, em revisão, obsoleto.
• Tipo de fonte: normativo, contratual ou informal, conforme a "Classificação" no cabeçalho do documento.
Nada entra ou some em silêncio. Documento com extração de texto falha (ex.: PDF escaneado) ou sem metadados não é indexado e aparece no relatório de ingestão.

**Tratamento de documentos contraditórios:**
O assistente aplica a regra de transição quando o documento a define, e mostra as duas versões quando não define. "Usar sempre a mais recente" está descartado: contradiz a PROC-042-v2, seção 5.
Detectar o conflito. Quando os trechos recuperados vêm de versões diferentes do mesmo documento, ou de fontes que dizem coisas diferentes sobre o mesmo item, o assistente deve entrar em "modo conflito" e avisar o atendente.
• Aceite: para "Qual o multiplicador para o Sudeste?", a resposta sinaliza que a v1 diz 1.0 e a v2 diz 1.1.

Nunca misturar versões num cálculo. Multiplicador, fator de peso e prazo adicional de uma mesma resposta vêm da mesma versão.
• Aceite: para 2.000 kg ao Sudeste em chamado novo, nenhuma resposta combina 1.1 (v2) com 1.2 (v1). Teste da armadilha 1 do Anexo B.
Documento oficial prevalece sobre o FAQ. Havendo divergência entre um documento normativo ou contratual e o FAQ, vale o oficial, e a divergência é mostrada.
• Casos: FAQ-45 ("desconto automático acima de 10 fretes") × PROC-042-v2, seção 4 (5% a partir de 8). FAQ-38 (registro em 48h, Jurídico) × POL-001, seção 3.5 (avaria em trânsito é devolução sem custo).
• Aceite: nos dois casos, a resposta abre com a regra oficial e cita o FAQ apenas como divergência.
CON-06. Todo conflito vira registro. Cada conflito detectado é registrado com as fontes envolvidas e enviado à curadoria, para a área dona decidir.
• Aceite: após rodar o conjunto de testes da seção 7, o registro contém os conflitos PROC-042 × v2 e FAQ-45 × v2.
A hierarquia entre documento normativo e contratual, se os dois se contradisserem, ainda não está definida (pergunta em aberto).

**Comportamento quando não há resposta na base:**
Quando a base não responde, o assistente diz "não encontrei", explica o que achou de mais próximo e por que não serve, e sugere escalar ao supervisor. Ele nunca completa a resposta com conhecimento geral. O mesmo vale para três casos que parecem ter resposta, mas não têm: cobertura parcial, documento fora da base e premissa falsa.
Quando a base não responde, o assistente diz "não encontrei", explica o que achou de mais próximo e por que não serve, e sugere escalar ao supervisor. Ele nunca completa a resposta com conhecimento geral. O mesmo vale para três casos que parecem ter resposta, mas não têm: cobertura parcial, documento fora da base e premissa falsa.
SR-01. Limite do conhecimento geral. O modelo pode usar conhecimento geral para entender a pergunta e redigir a resposta. Ele não pode usá-lo para regras, prazos, valores, percentuais, contatos ou procedimentos da NovaTech. Toda inferência que não está num trecho deve ser declarada.
• Caso: "Manaus" → região Norte não está em nenhum documento. A resposta diz "considerando Manaus na região Norte" para o atendente confirmar.
• Aceite: em todo o conjunto de testes, nenhum prazo, valor ou contato aparece sem estar num trecho citado (verificado por RA-03).
SR-02. Resposta padrão de "não encontrei". Deve conter: (a) a frase explícita de que não encontrou; (b) o documento mais próximo, se houver, e por que não se aplica; (c) a sugestão de escalar ao supervisor (guardrail 3).
• Aceite: para "Frete para 300 kg para Salvador?", a resposta tem os três elementos e nenhum valor.
SR-03. Cobertura parcial conta como "não encontrado". Um trecho parecido, mas fora do escopo que o próprio documento declara, não autoriza resposta.
• Caso: a PROC-042-v2 vale para cargas acima de 500 kg (seção 1). O trecho PROC-042v2-B aparece para 300 kg (mapa de cobertura do Anexo B), mas não se aplica.
• Aceite: nenhum multiplicador da PROC-042 aparece em resposta sobre carga até 500 kg. Teste da armadilha 5 do Anexo B.
SR-04. Documento citado, mas fora da base. Quando um trecho remete a outro documento que não está indexado, o assistente informa o nome do documento e que ele não está disponível.
• Casos: carga perigosa acima de 500 kg → PROC-043 (PROC-042-v2, seção 4). Mercadoria em trânsito → PROC-088 (POL-001, seção 2).
• Aceite: para frete de carga perigosa de 800 kg, a resposta não aplica os multiplicadores gerais e cita a PROC-043 como fora da base.
SR-05. Premissa falsa é corrigida, não respondida. Se a pergunta traz algo que a base desmente, o assistente corrige a premissa com a fonte.
• Caso: "Qual o SLA do cliente Platinum?" → a SLA-2024, seção 1, diz que só existem Gold, Silver e Standard.
• Aceite: nenhuma resposta atribui SLA a um tier inexistente. Teste da armadilha 3 do Anexo B.

**Requisitos de atualização:**
A atualização é automática, tem prazo contado a partir da publicação na fonte e vale também para retiradas. Operações, Compliance e Comercial publicam todo mês, e o Confluence muda toda semana: sem prazo, o assistente responde com a versão anterior sem ninguém perceber.

**Requisitos de rastreabilidade:**
Toda resposta cita a fonte e mostra o trecho literal, e todo número da resposta tem de estar no trecho citado. Isso permite ao atendente conferir em segundos (etapa P5 da jornada) e ao QA verificar de forma automática.
Citação completa em toda resposta com regra. Toda resposta que traz regra, prazo, valor ou contato cita: documento, versão, data, seção e tipo de fonte.
```

**Feedback recebido do Claude:**
```
Sua v1 já está boa em contradições e em "não encontrei". Antes da v2, precisa corrigir três coisas: o item 1 contradiz o item 2, o requisito de atualização não existe (só tem a justificativa) e várias referências apontam para trechos que não estão no texto.

Gravidade alta

O item 1 exclui justamente os documentos que o item 2 usa. Pela regra "indexa apenas documentos com metadados completos", três documentos ficariam fora da base:
a PROC-042 v1 não tem status nem classificação;
a PROC-042-v2 também não tem classificação;
o FAQ não tem versão ("não controlada") nem dono ("nenhum responsável formal").
Sem eles, o modo conflito e a regra "oficial prevalece sobre o FAQ" nunca seriam acionados. Sugestão: diga quem preenche o metadado que falta (a curadoria) e quais são os valores padrão, como "vigência não confirmada", "tipo não declarado" e "informal, sem dono".
O item 1 não responde à pergunta do exercício. O exercício pede quais fontes indexar e quais não. A v1 define metadados, mas não decide nada sobre SharePoint, Confluence (~400 páginas), planilhas (inclusive a tabela mensal de fretes), FAQ, PROC-042 v1 ou PROC-043/PROC-088. Sugestão: uma tabela com fonte, decisão e motivo.
Atualização (seu item 5) não tem requisito. O texto explica por que precisa de prazo, mas não diz qual é o prazo, quando o relógio começa, se retiradas seguem o mesmo prazo nem como o QA verifica. É a pergunta 4 do exercício ("em quanto tempo"), e hoje ela está sem resposta. Sugestão: prazo marcado como [PROPOSTA], início do relógio na publicação na fonte, retirada no mesmo prazo, ingestão automática e um aceite.
A curadoria quase não aparece. É o primeiro critério de avaliação, mas a palavra surge só uma vez (CON-06). Faltam dono por documento (os cabeçalhos já dizem quem é), quem faz a triagem e quais decisões precisam sair antes do go-live. Sugestão: uma seção própria.

Gravidade média

A regra de transição é citada, mas não especificada. Falta dizer que o assistente pergunta a data do chamado (antes ou depois de 01/12/2023). Falta também o que acontece com o que a seção 5 não cobre: fator de peso, prazo +2/+3 e desconto. O aceite do "Sudeste" diz que a resposta sinaliza 1.0 e 1.1, mas não diz qual valor ela aplica.
Só-FAQ em tema crítico não é tratado. A regra "oficial prevalece" só vale quando há conflito. No FAQ-32 (carga perigosa com expresso) não existe documento oficial para comparar, então o caso fica sem regra. Sugestão: um SR específico para isso.
Falta o oposto do "não encontrei". Não há requisito contra recusa indevida (a informação está no trecho e o assistente diz que não achou). Também falta registrar cada "não encontrei" para a curadoria.
Rastreabilidade incompleta. O único requisito lista os campos da citação, mas não tem aceite. Duas regras aparecem só no parágrafo de abertura e não viram requisito: mostrar o trecho literal (pergunta 5 do exercício) e exigir que todo número esteja no trecho citado.

Forma

Referências a trechos que não existem no texto: RA-03, "seção 7", "relatório de ingestão", "etapa P5" e "guardrail 3". O QA não consegue seguir.
Numeração e códigos inconsistentes. Os itens vão 1, 2, 3, 5, 7. Alguns requisitos têm código (CON-06, SR-01 a SR-05) e outros não, como "Detectar o conflito", "Nunca misturar" e todo o item 1.
O parágrafo de abertura do item 3 aparece duas vezes.

Os pontos 1 e 3 são os que mais pesam, porque tornam a especificação inconsistente e incompleta nos próprios critérios do exercício.

Quer que eu registre sua v1 e esta revisão no documento, no lugar das abas de histórico atuais? Assim o histórico mostra o seu rascunho, e não o meu.
```

## Especificação — versão refinada (v2)

# Especificação de Requisitos — Pipeline de RAG do Assistente de Atendimento NovaTech

**Autor:** Jaqueline Santos, Product Specialist
**Base:** achados da fase de discovery (mapa de temas, análise de inconsistências PROC-042 v1/v2, cruzamento com o FAQ-Atendimento, mapa de riscos)
**Público:** não técnico, mas com critérios verificáveis por QA

## Contexto

Esta especificação traduz os riscos já identificados no discovery — duas versões contraditórias do PROC-042, um FAQ informal sem validação oficial, e ausência de metadado de vigência documental — em requisitos concretos para o pipeline de RAG (a camada que decide o que o assistente pode consultar e como ele cita o que encontrou). Cada requisito vem com um critério de teste que o QA pode executar sem precisar entender a arquitetura interna do sistema.
O assistente só é confiável se a base for curada: cada documento indexado precisa ter dono, versão, vigência e tipo de fonte, e o assistente precisa saber o que fazer quando esses dados faltam ou se contradizem. 

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

O valor do prazo de ingestão (RF4.1/RF4.2) ainda não tem número confirmado pela NovaTech — está proposto como baseline de 24h úteis. A definição de quem tem autoridade formal para marcar um documento como obsoleto (RF1.5) também depende de uma decisão de governança que ainda não foi validada com Operações/Comercial/Compliance.

## Entregável

- [ ] Especificação final (v2)
- [ ] Histórico de iteração (v1 → feedback do Claude → v2)

