# Etapa 3 — Cruzamento com o FAQ-Atendimento

## Prompt enviado ao Claude

```
Aqui está a análise de inconsistências que fizemos entre PROC-042 v1 e v2, e o mapa
de temas da primeira etapa. Agora seguem as 9 perguntas do FAQ-Atendimento
completo. Cruze as informações: onde o FAQ reforça, contradiz ou complementa o que já identificamos? Que práticas aparecem no FAQ sem respaldo em nenhum documento formal?

[output da etapa 1 - Com base só nos metadados, dá para montar um mapa preliminar e algumas hipóteses fortes de risco — vale destacar que são hipóteses a validar quando eu vir o conteúdo e, principalmente, na entrevista.

Mapa de temas cobertos

Os cinco documentos cobrem quatro áreas temáticas, mas de forma desigual: devolução de mercadorias (POL-001), precificação de frete especial (PROC-042 e sua revisão), compromissos de atendimento por tier de cliente (SLA-2024), e práticas informais de suporte (FAQ-Atendimento). Note que há uma sobreposição de dono entre Comercial e Operações (frete é Comercial, devolução é Operações, SLA é dos dois), o que já sinaliza que o processo ponta a ponta atravessa mais de uma diretoria sem que nenhum documento pareça ser o "dono" da jornada completa do cliente.

Hipóteses de gaps e riscos

Governança de versões: PROC-042 existe em duas versões formalmente distintas (v1.0 e v2.0) catalogadas como documentos separados, não como histórico de um único documento. Isso sugere hipótese de risco real: pode não haver processo claro de deprecação, e times diferentes (ou sistemas/planilhas antigas) podem ainda estar usando a v1.0 sem saber que foi revisada. Vale perguntar na entrevista o que motivou a revisão — erro de cálculo, reclamação de cliente, mudança de mercado — porque isso costuma revelar o problema real que gerou a v2.0.

Fronteira entre devolução e frete especial: POL-001 trata de devolução de mercadorias em geral, e PROC-042 trata de frete para cargas acima de 500kg, mas nenhum título sugere uma política de logística reversa para cargas pesadas — ou seja, o que acontece quando uma carga de 600kg precisa ser devolvida? Quem calcula o frete de retorno, e sob qual regra? Isso é um gap clássico de fronteira entre políticas de diretorias diferentes, e tende a aparecer como "zona cinzenta" que cada área resolve do seu jeito.

SLA sem lastro operacional visível: SLA-2024 promete prazos de resposta/resolução por tier, mas nenhum dos outros documentos parece amarrar explicitamente os prazos internos das operações (aprovação de devolução, recálculo de frete) a esses SLAs. Hipótese: o SLA pode ter sido definido pelo Comercial como promessa comercial sem validação plena da capacidade operacional de cumpri-lo — um risco comum quando o dono é uma junção de duas diretorias em vez de uma área única de processo.

Segmentação de cliente não documentada à parte: SLA-2024 depende de "tipo de cliente"/tier, mas não há, entre os cinco documentos, um documento que defina os critérios dessa segmentação. Pode existir em outro lugar (CRM, contrato) — vale confirmar, porque se não existir formalmente, o SLA pode estar sendo aplicado de forma subjetiva.

FAQ-Atendimento como "política paralela": este é provavelmente o ponto de maior risco do lote. É o único documento sem dono formal, sem controle de versão, colaborativo — ou seja, é onde moram as exceções, os atalhos e o conhecimento tácito do time de suporte. Hipótese forte: o FAQ provavelmente contradiz ou complementa POL-001 e SLA-2024 em pontos que a diretoria não sabe que existem — é comum esse tipo de documento carregar a "política real" usada no dia a dia, criada para preencher lacunas dos documentos formais. Isso é ouro para a discovery, mas também é o maior risco de compliance/inconsistência com o cliente.

Maturidade desigual entre documentos: POL-001 já está na v3.1 (bastante amadurecida), enquanto PROC-042 está apenas na v2.0 e o FAQ nem tem controle de versão. Isso sugere que a organização tem disciplina de gestão documental na área de Operações (retorno/devolução), mas menos disciplina em Comercial e nenhuma em Atendimento — um padrão que vale confirmar na entrevista, pois costuma refletir onde estão os processos mais maduros de verdade.

Perguntas sugeridas para a discovery

Vale perguntar diretamente: o que mudou entre PROC-042 v1.0 e v2.0, e a v1.0 ainda circula em algum lugar? Existe algum caso de devolução de carga >500kg, e qual documento rege o frete desse retorno? O SLA foi validado com quem executa (Operações) antes de publicado, e existe medição real de aderência a ele? Quanto do que está no FAQ diverge do que está em POL-001 ou SLA-2024 — e há casos em que o time de suporte segue o FAQ sabendo que contraria a política formal?

Quando você mandar o conteúdo completo, posso validar essas hipóteses uma a uma e mapear com mais precisão onde estão as inconsistências reais entre os documentos.]

[output da etapa 2 - Inconsistências identificadas entre PROC-042 v1.0 e v2.0
Item    v1.0 (03/03/2023)    v2.0 (10/11/2023)    Direção da mudança
Fator de peso — 1.001kg a 3.000kg    1,2    1,15    queda
Fator de peso — acima de 3.000kg    1,5    1,4    queda
Multiplicador regional — Sul    1,2    1,3    alta
Multiplicador regional — Sudeste    1,0    1,1    alta
Multiplicador regional — Centro-Oeste    1,3    1,4    alta
Multiplicador regional — Nordeste    1,4    1,5    alta
Multiplicador regional — Norte    1,6    1,8    alta
Prazo adicional para carga pesada    +2 dias úteis    +3 dias úteis    alta
Desconto por volume — gatilho    >10 fretes/mês, negociado caso a caso, via aditivo contratual    ≥8 fretes/mês → 5% sobre o multiplicador regional; >15 fretes/mês → 10%    mudança de natureza (negociação → regra fixa), não só de número
Nota sobre PROC-043 (cargas perigosas)    Não existe    Adverte que a PROC-043 está em revisão pelo Compliance    só existe na v2
Regra de transição (seção 5)    Não existe    Chamados abertos antes de 01/12/2023 usam multiplicadores da v1; a partir dessa data, usam a v2    só existe na v2

Ficaram iguais entre as duas versões: a estrutura da fórmula (Valor base × Multiplicador regional × Fator de peso), as faixas de peso (500–1.000kg / 1.001–3.000kg / acima de 3.000kg — só os multiplicadores dentro delas mudaram), e a exigência de aprovação prévia para cargas acima de 5.000kg.

Risco de um assistente de IA misturar as duas versões

Eu classificaria esse risco como alto, e por motivos bem específicos ao conteúdo, não apenas porque "existem duas versões":

Os números são próximos o suficiente para não soarem um erro. 1,2 vs. 1,3, ou 1,4 vs. 1,5, são o tipo de diferença que passa despercebida numa resposta gerada — diferente de um erro grosseiro que o próprio texto entregaria como incoerente. Um assistente pode compor uma resposta plausível e internamente "sem contradições visíveis" mesmo pegando o multiplicador regional da v2 com o fator de peso da v1, por exemplo.

A direção da mudança não é uniforme. O multiplicador regional subiu na v2, mas o fator de peso caiu. Se o assistente usar uma heurística ingênua do tipo "a versão mais recente costuma ter valores mais altos, então valores mais altos = mais atual", ele vai acertar o multiplicador regional e errar o fator de peso (ou vice-versa) — produzindo um cálculo que nunca existiu em nenhuma das duas versões.

O desconto por volume não é só uma mudança de número, é uma mudança de mecanismo: de negociação caso a caso (v1) para regra percentual automática (v2). Misturar aqui é o pior cenário: por exemplo, aplicar o gatilho de 8 fretes/mês (v2) mas tratar como "precisa de aditivo contratual" (v1), ou aplicar desconto de 5% a partir de 10 fretes (mistura de limiar v1 com percentual v2) — nenhuma combinação híbrida corresponde a uma regra real da empresa.

O prazo de entrega (+2 vs. +3 dias) é uma promessa direta ao cliente. Um erro aqui vira risco de SLA descumprido ou de expectativa incorreta passada ao cliente — não é um erro só "interno".

O ponto mais crítico, porém, é a seção 5 da v2 (disposições transitórias). Ela informa que a resposta correta não depende apenas de "qual versão é a vigente", mas da data de abertura do chamado: chamados abertos antes de 01/12/2023 e ainda em processamento devem usar os multiplicadores da v1. Isso significa que mesmo um assistente que identificasse corretamente "a v2 é a mais nova, devo usar ela" ainda estaria errado para uma parcela dos casos reais — a v1 continua sendo a resposta certa em determinadas situações, indefinidamente, até que esses chamados antigos se encerrem (não há data-limite para isso). Nenhum dos dois documentos, isoladamente, resolve completamente a pergunta "qual multiplicador eu uso agora" sem essa terceira variável (data de abertura do chamado).

Some a isso o fato de que nenhum dos dois documentos declara formalmente que substitui o outro — ambos dizem textualmente que coexistem sem hierarquia clara no SharePoint. Isso é o cenário clássico de risco em base de conhecimento para IA (RAG): uma busca por "cálculo de frete especial" tende a recuperar as duas versões como igualmente relevantes (títulos quase idênticos, mesma estrutura, mesmo objetivo), e sem um sinal de metadado do tipo "vigente/obsoleto", o modelo não tem como discriminar sozinho — ele pode responder combinando trechos de ambas sem perceber, especialmente se a pergunta do usuário não mencionar a data do chamado.

Uma mitigação natural para quando vocês montarem o assistente de discovery/atendimento: tratar documentos conflitantes sem data de vigência explícita como uma condição bloqueante (o assistente deveria se recusar a responder com um número específico e sinalizar a ambiguidade, ou perguntar a data do chamado antes de calcular), em vez de silenciosamente escolher uma versão. Isso também é, em si, uma descoberta de processo: a NovaTech precisa de um dono formal de vigência documental antes de qualquer IA poder responder com segurança sobre esse cálculo.]

[FAQ-Atendimento completo - # FAQ-Atendimento — Perguntas Frequentes do Time de Suporte

**Versão:** Não controlada
**Última atualização:** Diversas (documento colaborativo)
**Responsável:** Nenhum responsável formal — mantido informalmente pelo time de atendimento
**Classificação:** Documento informal — NÃO validado por Compliance ou Operações. Representa o conhecimento prático do time, mas pode conter informações desatualizadas ou imprecisas.

Aviso interno: Este FAQ foi criado organicamente pelo time de atendimento ao longo de 2 anos. As respostas refletem a experiência prática dos atendentes, mas NÃO foram validadas contra os documentos oficiais (POL, PROC, SLA). Use com cautela e sempre confirme informações críticas na documentação normativa.

## Perguntas selecionadas (das 47 do documento original)

### Item 3 — "Cliente perguntou se pode devolver carga perigosa. O que respondo?"
Na prática, a gente orienta o cliente a ligar no ramal 4500 (Gestão de Riscos). Oficialmente não pode pelo processo padrão, mas já tiveram casos em que o pessoal de Riscos autorizou exceção. Então não diga que é impossível — diga que precisa de tratamento especial.

### Item 8 — "Como funciona o frete especial?"
Acima de 500kg, aplica a tabela de multiplicadores por região. Cuidado: existem duas versões da PROC-042. A mais recente tem multiplicadores mais altos. Na dúvida, use a mais recente (v2), mas se o cliente reclamar do valor, pode ser que o contrato dele ainda esteja na tabela antiga.

### Item 15 — "Cliente diz que é Platinum. Existe esse tier?"
Não existe tier Platinum na NovaTech. Às vezes o cliente confunde com outra transportadora ou com o programa de fidelidade antigo que foi descontinuado em 2022. Oriente que nossos tiers são Gold, Silver e Standard e peça o número do contrato para verificar.

### Item 22 — "Cliente quer saber sobre seguro de carga. O que falar?"
A NovaTech oferece seguro de carga como adicional. O valor é 0,3% do valor declarado da mercadoria para cargas padrão e 0,8% para cargas perigosas. Detalhe: isso vale para contratos a partir de 2023. Contratos mais antigos podem ter percentuais diferentes — confirme com o Comercial.

### Item 27 — "O tracking mostra 'em trânsito' há 5 dias. O que faço?"
Depende da rota. Rotas para o Norte podem levar até 10 dias úteis. Para Sul/Sudeste, mais de 3 dias parado é estranho. Abra um chamado de rastreamento e classifique como prioridade alta se for Gold ou se o valor da carga for acima de R$ 50.000.

### Item 32 — "Pode enviar carga perigosa com frete expresso?"
Sim, mas precisa de autorização do Compliance e a documentação ANTT tem que estar atualizada. Na prática, demora uns 2 dias para conseguir a autorização, então o 'expresso' acaba não sendo tão expresso. Avise o cliente sobre isso.

### Item 38 — "Cliente quer saber a política para carga que chegou danificada."
Carga danificada em trânsito tem processo diferente de devolução. O cliente precisa registrar a ocorrência em até 48h após o recebimento, com fotos e laudo se possível. A NovaTech investiga e, se comprovada responsabilidade nossa, reembolsa integralmente. Mas isso passa pelo Jurídico, não pelo atendimento normal — encaminhe para o e-mail sinistros@novatech.com.br.

### Item 41 — "Qual a diferença entre SLA de resposta e SLA de resolução?"
Resposta é quando a gente dá o primeiro retorno ao cliente (mesmo que seja 'estamos verificando'). Resolução é quando o problema é efetivamente resolvido. O Gold tem 2h de resposta e 24h de resolução. Silver é 4h e 48h. Standard é 8h e 72h. Para incidentes críticos, os prazos são menores — veja a tabela SLA-2024.

### Item 45 — "O cliente quer desconto no frete. Posso dar?"
Atendente não tem autonomia para dar desconto. Para clientes com mais de 10 fretes especiais por mês, existe desconto automático na tabela (veja PROC-042). Para outros casos, encaminhe ao Comercial com justificativa.]
```

## Output obtido

```
cruzamento do FAQ com as etapas 1 e 2

Resumo: o FAQ confirma a hipótese mais forte da Etapa 1. Ele funciona como uma política paralela. Dos 9 itens, só 2 são apenas consistentes com a documentação formal (15 e 41). Dois pioram o conflito da PROC-042 (8 e 45), porque criam regras híbridas que não existem em nenhuma das versões. Cinco trazem práticas sem nenhum documento formal por trás (22, 27, 32, 38 e a exceção do item 3).

Limite desta análise: neste contexto recebi o conteúdo completo só da PROC-042 (v1 e v2) e do FAQ. Para POL-001 e SLA-2024 tenho apenas metadados e resumos. As comparações com esses dois documentos se apoiam no que o próprio FAQ diz sobre eles e precisam ser confirmadas no texto original.

1. Onde o FAQ reforça, contradiz ou complementa
Item	Tema	Relação	O que muda frente às etapas 1 e 2
3	Devolução de carga perigosa	Suaviza a regra formal	O próprio FAQ admite que "oficialmente não pode", mas orienta o atendente a não dizer isso ao cliente. A regra vira "tratamento especial".
8	Frete especial	Contradiz a v2 e piora o conflito	Cria um terceiro critério de versão, "o contrato do cliente", que não existe nos documentos.
15	Tier Platinum	Reforça	Confirma que só existem Gold, Silver e Standard. Diz que a verificação é feita pelo número do contrato.
22	Seguro de carga	Complementa, sem respaldo	Informa percentuais (0,3% e 0,8%) que não aparecem em nenhum documento formal.
27	Tracking parado	Complementa, sem respaldo	Traz prazos por rota e um critério próprio de prioridade alta.
32	Carga perigosa com frete expresso	Complementa, sem respaldo	Descreve um fluxo de autorização pelo Compliance que não está documentado.
38	Carga danificada	Complementa, sem respaldo	Descreve um processo completo (prazo, Jurídico, e-mail) que só existe no FAQ.
41	SLA de resposta e de resolução	Reforça (confirmar no SLA-2024)	Os números (2h/24h, 4h/48h, 8h/72h) batem com o resumo do SLA-2024.
45	Desconto no frete	Mistura as duas versões	Junta o gatilho da v1 com o mecanismo da v2.
2. Cruzamento com as inconsistências da Etapa 2 (PROC-042)

O FAQ não resolve nenhuma das inconsistências. Em dois pontos, ele agrava.

Item 8: materializa a heurística ingênua prevista na Etapa 2

O FAQ diz que "a mais recente tem multiplicadores mais altos". Isso vale para o multiplicador regional, mas esconde que o fator de peso caiu na v2 (1,2 para 1,15 e 1,5 para 1,4). Um atendente ou um assistente que siga essa lógica pode combinar o multiplicador da v2 com o fator de peso da v1. É exatamente o cálculo híbrido que a Etapa 2 apontou como risco alto.
O FAQ diz que, "se o cliente reclamar, pode ser que o contrato dele esteja na tabela antiga". Isso inventa um critério por contrato. A seção 5 da v2 define a transição pela data de abertura do chamado, antes ou depois de 01/12/2023. Há agora três regras concorrentes: a v1, a v2 e a regra do FAQ.
O preço passa a depender de o cliente reclamar ou não. Quem reclama pode cair na tabela mais barata, e quem não reclama paga a v2. Isso gera tratamento desigual e é um risco comercial.

Item 45: cria uma regra que nunca existiu

O limite "mais de 10 fretes por mês" vem da v1, onde o desconto era negociado caso a caso por aditivo contratual.
O "desconto automático na tabela" é o mecanismo da v2, onde o gatilho é a partir de 8 fretes (5%) e acima de 15 (10%).
Na prática, clientes com 8 a 10 fretes por mês têm direito ao desconto pela v2, mas o FAQ manda negar. O FAQ também não cita os percentuais nem o degrau de 10%.
O item ainda aponta "veja PROC-042" sem dizer a versão. Se o assistente indexar o FAQ, esse item recupera junto os chunks das duas versões.

O que o FAQ não cobre

O FAQ não fala do prazo adicional (+2 ou +3 dias úteis). Esse conflito continua sem nenhuma orientação prática, e é o que vira promessa direta ao cliente.
O FAQ não fala da transição por data de chamado. Ou o time não conhece a seção 5 da v2, ou não a aplica.
3. Validação das hipóteses da Etapa 1
Hipótese da Etapa 1	Resultado	Evidência no FAQ
FAQ como "política paralela"	Confirmada	Itens 3, 8, 27 e 45 alteram ou substituem regras formais
Governança de versões fraca (PROC-042)	Confirmada e ampliada	O time sabe que existem duas versões (item 8) e improvisa um critério próprio
Fronteira entre devolução e frete especial (logística reversa acima de 500kg)	Continua sem resposta	Nenhum item trata do frete de retorno de carga pesada
SLA sem lastro operacional	Parcialmente confirmada	O item 27 cria um critério interno de prioridade (Gold ou carga acima de R$ 50.000) que não está nos documentos formais
Segmentação de clientes não documentada	Enfraquecida	O item 15 indica que o tier é verificado pelo contrato. Os critérios precisam ser confirmados no SLA-2024
Maturidade desigual entre áreas	Confirmada	Os temas mais críticos que só existem no FAQ (sinistro, seguro, carga perigosa expressa) dependem de Jurídico, Comercial e Compliance

Novo achado: carga perigosa é um cluster de risco. Ela aparece em três pontos, e nenhum deles tem uma regra formal estável:

Devolução (item 3): a regra formal é suavizada no dia a dia.
Frete expresso (item 32): a autorização do Compliance não está documentada.
Frete acima de 500kg (Etapa 2): a PROC-043 está em revisão pelo Compliance.

É o tema com maior risco regulatório (ANTT) e o que tem a base mais fraca.

4. Práticas sem respaldo em documento formal
Item 3: a Gestão de Riscos já autorizou exceções para devolução de carga perigosa. O FAQ não diz quais critérios foram usados.
Item 8: vale a tabela antiga para contratos antigos.
Item 15: existiu um programa de fidelidade antigo, descontinuado em 2022. É um dado histórico, sem fonte.
Item 22: seguro de 0,3% para carga padrão e 0,8% para carga perigosa, para contratos a partir de 2023.
Item 27: rotas para o Norte levam até 10 dias úteis. Para Sul e Sudeste, mais de 3 dias parado é anormal. Prioridade alta para cliente Gold ou carga acima de R$ 50.000.
Item 32: carga perigosa com frete expresso exige autorização do Compliance, que leva cerca de 2 dias.
Item 38: carga danificada tem registro em até 48h, laudo, reembolso integral, passa pelo Jurídico e é encaminhada para sinistros@novatech.com.br.
Item 45: desconto automático para quem faz mais de 10 fretes por mês.
5. O que o assistente faria de errado se indexasse o FAQ com o mesmo peso
Item	Resposta errada provável	Gravidade
3	"Carga perigosa pode ser devolvida com tratamento especial", invertendo a regra	Alta: regulatória
8	Calcula com uma versão escolhida pelo "contrato" ou mistura parâmetros das duas	Alta: financeira
45	Nega desconto a quem tem 8 a 10 fretes por mês, ou promete desconto automático sem percentual	Alta: financeira e contratual
22	Informa percentuais de seguro como se fossem oficiais	Média a alta: contratual
38	Promete "reembolso integral" como regra, quando depende de investigação do Jurídico	Alta: jurídica
27	Classifica prioridade com um critério que difere da definição formal de incidente crítico	Média: SLA
32	Confirma frete expresso para carga perigosa sem um processo formal por trás	Alta: regulatória
6. Perguntas para o discovery humano
Diretoria Comercial: a v1 está revogada? Contratos assinados sob a v1 mantêm a tabela antiga, ou vale só a data do chamado? O desconto do item 45 (mais de 10 fretes) está sendo aplicado hoje? Os percentuais de seguro do item 22 são oficiais?
Compliance e Gestão de Riscos: quais foram os critérios das exceções de devolução de carga perigosa (item 3)? Existe um fluxo formal para carga perigosa com frete expresso? Qual é o prazo previsto para a revisão da PROC-043?
Jurídico: o processo de sinistro do item 38 (48h, laudo, reembolso integral) é oficial? Pode ser informado ao cliente pelo atendimento?
Operações: de onde vêm os prazos de rota do item 27? O critério de R$ 50.000 conversa com a definição de incidente crítico do SLA-2024?
Atendentes e supervisores: quando o FAQ e o documento formal divergem, qual dos dois vocês seguem? Quem atualiza o FAQ, e com que frequência?

Notas sobre o seu prompt, para usar no entregável:

O que funcionou bem: passar os outputs das etapas 1 e 2 antes do FAQ. Isso permitiu validar hipóteses e reconhecer as regras híbridas previstas na Etapa 2.
Ponto de melhoria: o prompt não marcou o FAQ como fonte de baixa confiabilidade e não incluiu os trechos-chave de POL-001 e SLA-2024. Por isso, os itens 3, 15, 27 e 41 só puderam ser comparados com o que o próprio FAQ diz sobre esses documentos. Se você rodar de novo, vale acrescentar duas ou três linhas com a regra formal de cada um e pedir categorias fixas de classificação. O Prompt 3 do guia já traz essa versão.

Se quiser, adiciono esta resolução ao documento da prática, na seção da Etapa 3.
