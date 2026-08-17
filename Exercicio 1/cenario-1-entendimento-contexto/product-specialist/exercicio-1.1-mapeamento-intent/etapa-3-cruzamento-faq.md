# Etapa 3 — Cruzamento com o FAQ-Atendimento

## Prompt enviado ao Claude

```
<!-- Aqui está a análise de inconsistências que fizemos entre PROC-042 v1 e v2, e o mapa
de temas da primeira etapa. Agora seguem as 8 perguntas do FAQ-Atendimento
completo. Cruze as informações: onde o FAQ reforça, contradiz ou complementa o que
já identificamos? Que práticas aparecem no FAQ sem respaldo em nenhum documento
formal?
[output da etapa 1 - <!-- Com base só nos metadados, dá para montar um mapa preliminar e algumas hipóteses fortes de risco — vale destacar que são hipóteses a validar quando eu vir o conteúdo e, principalmente, na entrevista.
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
Quando você mandar o conteúdo completo, posso validar essas hipóteses uma a uma e mapear com mais precisão onde estão as inconsistências reais entre os documentos.-->]

[output da etapa 2 - # PROC-042-v2 — Procedimento de Cálculo de Frete Especial (Revisado)
**Versão:** 2.0
**Data de emissão:** 10/11/2023
**Responsável:** Diretoria Comercial
**Status:** Este documento não possui indicação formal de que substitui o PROC-042 v1. Ambos coexistem no SharePoint sem hierarquia clara.
## 1. Objetivo
Definir a fórmula e os parâmetros atualizados para cálculo de frete especial aplicável a cargas com peso acima de 500kg. Os multiplicadores foram revisados para refletir os custos operacionais atualizados de cada região.
## 2. Fórmula de cálculo
O frete especial é calculado como:
Valor do frete = Valor base × Multiplicador regional × Fator de peso
Onde:
- Valor base = tarifa publicada na tabela mensal de fretes.
- Multiplicador regional = fator aplicado conforme a região de destino (seção 2.1).
- Fator de peso = 1.0 para cargas de 500kg a 1.000kg; 1.15 para cargas de 1.001kg a 3.000kg; 1.4 para cargas acima de 3.000kg.
### 2.1. Multiplicadores regionais (atualizados em novembro/2023)
| Região | Multiplicador |
|--------|--------------|
| Sul | 1.3 |
| Sudeste | 1.1 |
| Centro-Oeste | 1.4 |
| Nordeste | 1.5 |
| Norte | 1.8 |
## 3. Prazo de entrega para frete especial
O prazo de entrega para frete especial é calculado como o prazo padrão da rota + 3 dias úteis adicionais para manuseio e roteirização de carga pesada (anteriormente era + 2 dias na versão anterior).
## 4. Condições especiais
- Cargas acima de 5.000kg requerem aprovação prévia do gerente de operações regional.
- Cargas perigosas com peso acima de 500kg seguem tabela específica (PROC-043: Frete de Cargas Perigosas). Nota: a PROC-043 está em processo de revisão pelo Compliance e pode sofrer alterações.
- Descontos de volume: a partir de 8 fretes especiais/mês para o mesmo cliente, aplicar desconto de 5% sobre o multiplicador regional. Acima de 15 fretes/mês, desconto de 10%. Descontos maiores requerem aprovação da Diretoria Comercial.
## 5. Disposições transitórias
Durante o período de transição, chamados abertos antes de 01/12/2023 que ainda estejam em processamento devem usar os multiplicadores da versão anterior (PROC-042 v1). Chamados novos a partir de 01/12/2023 devem usar os multiplicadores desta versão.] -->]
[FAQ-Atendimento completo, os 8 itens - 

```markdown
# FAQ-Atendimento — Perguntas Frequentes do Time de Suporte

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
Atendente não tem autonomia para dar desconto. Para clientes com mais de 10 fretes especiais por mês, existe desconto automático na tabela (veja PROC-042). Para outros casos, encaminhe ao Comercial com justificativa.] -->
```

## Output obtido

```
<!-- Cruzamento FAQ × documentos formais

Antes de entrar no detalhe: dos cinco documentos, só temos o conteúdo completo de PROC-042 v1/v2. POL-001 e SLA-2024 continuam sendo só metadados. Então, onde o FAQ toca esses dois, o que dá para fazer é comparar com o tema declarado (o resumo de uma linha), não com regras específicas — vou marcar isso claramente item a item.

Item do FAQ	Tema	Relação com o que já mapeamos	O que isso revela
3 — devolução de carga perigosa	Cruza POL-001 (devolução) + PROC-043 (não está no nosso lote)	Sem respaldo formal	"Oficialmente não pode, mas Riscos já autorizou exceção" — é uma prática de exceção via um canal (ramal 4500, Gestão de Riscos) que não aparece em nenhum dos 5 documentos. Confirma a hipótese da "fronteira devolução × carga especial" do mapa inicial, só que pior: a exceção já existe na prática, sem nenhuma regra escrita sobre quando ela se aplica.
8 — frete especial	PROC-042 v1/v2	Contradiz/confirma o risco que já apontamos	O FAQ reconhece as duas versões e instrui "na dúvida, use a mais recente (v2)" — exatamente a heurística ingênua que eu tinha sinalizado como perigosa na etapa anterior, porque ignora a regra de transição da seção 5 (que depende da data do chamado, não de "qual é mais nova"). Além disso, introduz um critério novo que não está em nenhuma das duas versões: "se o cliente reclamar, pode ser que o contrato dele ainda esteja na tabela antiga" — ou seja, um critério por data do contrato, diferente do critério oficial por data de abertura do chamado. Isso é uma terceira regra, não documentada, inventada para tapar um buraco.
15 — tier Platinum	SLA-2024 (só metadados)	Complementa (primeira info real sobre tiers)	Primeira confirmação do conteúdo de SLA-2024: tiers são Gold, Silver, Standard. Mas também revela um programa de fidelidade descontinuado em 2022 que ainda gera confusão de cliente — artefato histórico sem nenhum documento de encerramento formal entre os 5.
22 — seguro de carga	Nenhum documento do lote	Sem respaldo documental nenhum	Tema inteiro (seguro de carga, 0,3%/0,8%) não aparece em nenhum título do nosso lote. E de novo aparece o padrão "contratos antigos podem ter percentual diferente, confirme com o Comercial" — mesmo problema de regra que muda no tempo sem registro formal de transição.
27 — tracking parado	Toca SLA-2024 e frete geral, mas não é coberto por PROC-042 (que só trata frete especial >500kg)	Sem respaldo direto	Critério de priorização por valor de carga (>R$ 50.000) e por tier (Gold) para abrir chamado de rastreamento não está em nenhum documento formal que temos. Prazos regionais de trânsito (Norte até 10 dias, Sul/Sudeste "estranho" acima de 3) também não aparecem em nenhum título do lote.
32 — carga perigosa + frete expresso	Toca PROC-042 (menção à PROC-043) e implicitamente SLA-2024 (promessa de "expresso")	Reforça a hipótese "SLA sem lastro operacional"	O atendente já reconhece que a autorização de Compliance leva ~2 dias, tornando o "expresso" não expresso. É a primeira evidência concreta de que o compromisso comercial pode estar descolado da capacidade operacional real — hipótese que eu tinha levantado só como suspeita na etapa 1.
38 — carga danificada / sinistro	POL-001 é sobre devolução; isto é um processo distinto	Sem respaldo documental nenhum	Processo de sinistro (prazo de 48h, laudo, reembolso integral, encaminhado ao Jurídico via e-mail) é um processo inteiro que não parece coberto por POL-001 nem por nenhum outro documento do lote. Se POL-001 trata só de devolução "normal", carga danificada em trânsito pode ser uma lacuna real de política, não só de FAQ.
41 — SLA resposta vs. resolução	SLA-2024 (só metadados)	Complementa (primeiros números reais do SLA)	Confirma os tiers do item 15 e dá números específicos (Gold 2h/24h, Silver 4h/48h, Standard 8h/72h) supostamente vindos da "tabela SLA-2024". Não temos como validar contra o documento oficial ainda — é a palavra do FAQ sobre o FAQ, por assim dizer.
45 — desconto no frete	PROC-042 v1/v2	Contradição confirmada e mais grave do lote	O FAQ diz "mais de 10 fretes especiais/mês... desconto automático na tabela". O limiar ">10" é da v1 — mas a v1 não tem desconto automático em tabela, tem negociação caso a caso via aditivo contratual. O desconto automático em tabela é da v2 — mas lá o limiar é "a partir de 8", não "mais de 10". Ou seja, o FAQ já mistura as duas versões numa única resposta, pegando o número de uma e o mecanismo da outra — exatamente o modo de falha que eu tinha descrito como risco hipotético de IA na etapa anterior. Aqui ele já está acontecendo, feito por humanos, documentado por escrito.
Padrão sistêmico que atravessa vários itens

Um padrão se repete em pelo menos três pontos independentes do FAQ — frete especial (item 8: contrato antigo x tabela nova), seguro de carga (item 22: contratos anteriores a 2023 com percentual diferente) e tier de fidelidade (item 15: programa descontinuado em 2022 ainda causa confusão). Em todos os três, existe uma mudança de regra ao longo do tempo sem uma política formal de transição/grandfathering documentada — a única exceção parcial é a seção 5 da PROC-042 v2, que pelo menos tenta formalizar uma regra de transição (por data de chamado), mas que o próprio FAQ já reinterpreta incorretamente (por data de contrato, no item 8). Isso sugere que "regra muda, mas ninguém documenta como tratar o que já estava em andamento" pode ser um problema estrutural da NovaTech, não um incidente isolado da PROC-042.

O achado mais importante para levar à entrevista

O item 45 merece destaque isolado: não é uma hipótese de risco de IA, é uma inconsistência real e já materializada em um documento usado por humanos todos os dias. Isso muda o enquadramel da discovery — a pergunta não é mais "um assistente de IA poderia misturar as versões?", é "o time de atendimento já mistura as versões, e há quanto tempo isso afeta clientes reais?". Vale perguntar: quantos descontos já foram concedidos com base nesse critério híbrido do item 45, e existe alguma auditoria retroativa desses casos?

Perguntas adicionais para a discovery

Além das perguntas da etapa 1, valeria acrescentar: quem no Compliance/Gestão de Riscos tem autoridade formal para autorizar exceções de devolução de carga perigosa, e essa autoridade está documentada em algum lugar fora do FAQ? Existe uma política de seguro de carga formalizada, e por que ela não está entre os documentos já levantados? O processo de sinistro por carga danificada é tratado como parte da política de devolução (POL-001) ou é reconhecido pela empresa como um processo distinto — e nesse caso, existe uma política própria? Os critérios de priorização de chamados por valor de carga e por tier (item 27) vêm de algum documento oficial de operações, ou são um critério que o próprio time de atendimento criou com o tempo? E, por fim: quando o item 45 foi escrito no FAQ, isso foi antes ou depois da v2 da PROC-042 — ou seja, o FAQ nunca foi atualizado desde a revisão, ou foi atualizado errado? -->
```

## Observações

<!-- Onde o FAQ reforça, contradiz ou complementa

Reforça (confirma hipóteses já levantadas, sem introduzir uma regra nova conflitante):

Item 3 reforça a hipótese da "fronteira devolução × carga especial" do mapa inicial — e mostra que ela já é resolvida na prática por uma exceção informal (Gestão de Riscos autorizando caso a caso), não por uma regra escrita. Item 32 reforça diretamente a hipótese "SLA sem lastro operacional visível": o próprio atendente reconhece que a aprovação de Compliance leva ~2 dias, o que esvazia a promessa comercial de frete "expresso" para carga perigosa.

Contradiz (aqui a informalidade já produziu uma regra que não bate com nenhum documento oficial):

Item 45 é a contradição mais grave: mistura o limiar de volume da v1 (mais de 10 fretes/mês) com o mecanismo de desconto automático em tabela da v2 — uma combinação que não existe em nenhuma das duas versões oficiais da PROC-042. Item 8 também contradiz a regra oficial, mas de um jeito mais sutil: em vez de aplicar a regra de transição da v2 (que depende da data de abertura do chamado), o atendente usa um critério não autorizado — a data do contrato do cliente — para decidir qual tabela vale. É uma regra paralela, criada informalmente, que substitui a regra formal em vez de segui-la.

Complementa (preenche lacunas onde antes só tínhamos metadado, sem contradizer nada que já sabíamos):

Item 15 e item 41 são a primeira informação real sobre o conteúdo de SLA-2024 — antes tínhamos só o título e o resumo de uma linha. Agora sabemos que os tiers são Gold, Silver e Standard, e temos números específicos de prazo de resposta/resolução por tier. Importante: isso ainda não foi validado contra o texto oficial do SLA-2024, é a versão do FAQ sobre o SLA — fica como item a confirmar quando você mandar o conteúdo completo desse documento. Item 38 complementa (ou talvez extrapole) o escopo de POL-001: descreve um processo de sinistro por carga danificada em trânsito, tratado pelo Jurídico, que pode ser parte de POL-001 ou pode ser uma lacuna dela — só dá para saber com o texto completo de POL-001 em mãos.

Práticas sem respaldo formal em nenhum dos 5 documentos

Cinco práticas aparecem no FAQ sem nenhum documento do lote que as sustente: a autorização de exceção para devolução de carga perigosa via ramal da Gestão de Riscos (item 3); o produto de seguro de carga inteiro, com percentuais de 0,3%/0,8% e uma distinção entre contratos antes/depois de 2023 (item 22); os critérios de priorização de chamado de rastreamento por valor da carga acima de R$ 50.000 ou por tier Gold (item 27); o processo de sinistro por carga danificada, com prazo de 48h, laudo e reembolso integral via Jurídico (item 38); e o programa de fidelidade descontinuado em 2022, que ainda gera confusão de cliente sem que exista um documento formal de encerramento (item 15).

Vale notar que três dessas situações (item 8, item 22 e item 15) compartilham o mesmo padrão de fundo: uma regra mudou em algum momento (tabela de frete, percentual de seguro, estrutura de tiers) e ninguém formalizou como tratar o que ficou "para trás" — cada caso foi resolvido por um critério improvisado e diferente (data do contrato, "confirme com o Comercial", "peça o número do contrato"). Isso sugere que a ausência de regra de transição não é um problema isolado da PROC-042, é um padrão organizacional que vale investigar diretamente na entrevista. -->
