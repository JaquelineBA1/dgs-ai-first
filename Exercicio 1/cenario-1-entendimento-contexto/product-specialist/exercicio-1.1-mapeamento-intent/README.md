# Exercício 1.1 — Mapeamento de intent com engenharia de contexto

**Papel:** Product Specialist (Analista de Requisitos)
**Ferramenta utilizada:** Claude (chat)

## Contexto

Breve resumo do exercício: você vai pré-analisar a documentação da NovaTech
em 3 etapas progressivas (engenharia de contexto), antes de qualquer entrevista
com stakeholder, para gerar hipóteses e identificar riscos.

## Estratégia de contexto adotada

Se eu tivesse fornecido os cinco documentos completos de uma vez, a primeira leitura provavelmente teria pulado direto para as contradições de superfície mais óbvias (números diferentes, prazos diferentes) e teria dado menos peso a padrões estruturais que só aparecem quando você olha só para metadados — como a sobreposição de dono entre Comercial e Operações, ou o fato de duas versões do mesmo procedimento existirem como documentos catalogados separadamente em vez de um único histórico versionado. Isso só ficou visível na etapa 1 porque não havia conteúdo nenhum para distrair.

Testar hipóteses em vez de só descrevê-las. Cada etapa funcionou como um ciclo de hipótese → verificação. Na etapa 1 houve o levantamento da hipótese de que o FAQ provavelmente seria "política paralela" com práticas não respaldadas. Na etapa 3, isso deixou de ser hipótese e virou fato verificável: o item 45 mostrou uma mistura real de v1 e v2 acontecendo na prática, não apenas um risco teórico que tinha sido especulado. Se tivesse compartilhado o FAQ junto com tudo mais desde o início, essa confirmação teria menos força — seria só mais um dado entre muitos, e não a validação de uma hipótese que já tinha sido exposta para poder conferir depois.

Isolar variáveis para fazer um diff rigoroso. Ao compartilhar as duas versões da PROC-042 na etapa 2, sem os outros três documentos competindo por atenção, ficou possível fazer uma comparação linha a linha de verdade — pegar diferenças sutis como 1,2 vs. 1,3 ou 1,15 vs. 1,2, que são exatamente o tipo de coisa que se perde quando a atenção está espalhada por cinco documentos ao mesmo tempo. Essa mesma precisão foi o que permitiu, na etapa 3, reconhecer o item 45 como uma mistura exata de v1 e v2.

Esse desenho imita a dinâmica real de uma entrevista de Discovery, e ao mesmo tempo testa como um assistente de IA raciocina sob informação incremental — que é literalmente um dos riscos que estou mapeando (um assistente que mistura versões, ou que ancora em conclusões antes de ter o quadro completo). Numa discovery de verdade, documentos formais chegam primeiro e documentos informais como o FAQ aparecem depois, muitas vezes revelando que a prática real diverge do que está escrito. Estruturar a análise nessa mesma ordem serve tanto para me preparar para a entrevista quanto para observar, na prática, se as conclusões da etapa 1 se sustentaram, precisaram de ajuste, ou foram diretamente confirmadas — o que é, em si, uma forma de auditar a qualidade do próprio raciocínio incremental..

## Etapa 1 — Visão geral (metadados apenas)

**Por que forneci a informação nessa ordem:**
Optei por apresentar primeiro apenas os metadados — título, versão, responsável e resumo de uma linha — porque essa camada de informação já é suficiente para revelar riscos estruturais e de governança que ficam menos visíveis quando o conteúdo completo dos documentos está disponível. Sobreposição de responsáveis entre diretorias, proliferação de versões catalogadas como documentos distintos, ausência de controle formal de versão e desequilíbrio de maturidade documental são padrões que aparecem no nível dos metadados, e queria testar se essa camada, sozinha, já seria capaz de gerar hipóteses de risco relevantes antes de qualquer leitura de conteúdo.

Essa ordem também teve a função de estabelecer uma linha de base de hipóteses a ser confirmada, refinada ou descartada nas etapas seguintes — em vez de partir direto para contradições de superfície (números, prazos, fórmulas), que só se tornam visíveis com o conteúdo completo e que tendem a capturar a atenção primeiro, deixando os padrões estruturais em segundo plano. Ao fixar as hipóteses antes de ver o conteúdo, ficou possível avaliar depois, de forma mais rigorosa, se a análise documental confirmava ou contrariava o que a estrutura dos metadados já sugeria.

**Prompt utilizado:**
```
Tenho 5 documentos da NovaTech (empresa de logística) que preciso analisar antes de 
qualquer entrevista de discovery. Vou te passar só os metadados de cada um por
enquanto — título, versão, responsável e um resumo de uma linha — sem o conteúdo
completo. 
Com base só nisso, me dê um mapa dos temas cobertos e hipóteses de gaps ou riscos 
que já seja possível mapear, antes de eu te mostrar o conteúdo completo.
1. POL-001 — Política de Devolução de Mercadorias, v3.1, responsável: Diretoria de
   Operações. Define regras e prazos para devolução de mercadorias.
2. PROC-042 — Procedimento de Cálculo de Frete Especial, v1.0, responsável:
   Diretoria Comercial. Define fórmula e multiplicadores para frete de cargas
   acima de 500kg.
3. PROC-042-v2 — Procedimento de Cálculo de Frete Especial (Revisado), v2.0,
   responsável: Diretoria Comercial. Mesma finalidade do documento anterior, mas
   revisado.
4. SLA-2024 — Tabela de SLA por Tipo de Cliente, v2024.1, responsável: Diretoria
   Comercial + Operações. Define prazos de resposta/resolução por tier de cliente.
5. FAQ-Atendimento — Perguntas Frequentes do Time de Suporte, versão não
   controlada, sem responsável formal. Documento colaborativo e informal com
   práticas dos atendentes.
```

**Output obtido:**
```
Com base só nos metadados, dá para montar um mapa preliminar e algumas hipóteses fortes de risco — vale destacar que são hipóteses a validar quando eu vir o conteúdo e, principalmente, na entrevista.

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

Quando você mandar o conteúdo completo, posso validar essas hipóteses uma a uma e mapear com mais precisão onde estão as inconsistências reais entre os documentos.
 ```

**Como a qualidade do output foi (genérica? específica?):**
A qualidade foi genérica, em alguns pontos tentou especificar mas devido a falta de informações, acabou mencionando informações genéricas.

Ver também: [etapa-1-visao-geral.md](./etapa-1-visao-geral.md)

## Etapa 2 — Análise profunda (2 documentos selecionados)

**Documentos escolhidos e por quê:**
Escolhi os documentos PROC-042 v1 e v2, por serem as duas versões contraditórias identificadas na etapa 1. A v2 revisa a v1, mas não declara formalmente que a substitui, e as duas coexistem no SharePoint. Queria isolar esse conflito para um diff linha a linha e observar como a IA avalia o risco de misturar as versões.

**Prompt utilizado:**
```
Aqui estão os conteúdos completos do PROC-042 v1.0 e do PROC-042-v2 (revisado). Identifique todas as inconsistências entre as duas versões — valores, prazos, fórmulas, qualquer coisa que difira — e avalie o risco de um assistente de IA misturar as duas versões numa mesma resposta. 
[

```markdown
# PROC-042 — Procedimento de Cálculo de Frete Especial

**Versão:** 1.0
**Data de emissão:** 03/03/2023
**Responsável:** Diretoria Comercial
**Status:** Este documento não possui indicação formal de vigência ou obsolescência no sistema da NovaTech. Coexiste com a versão PROC-042-v2.

## 1. Objetivo

Definir a fórmula e os parâmetros para cálculo de frete especial aplicável a cargas com peso acima de 500kg.

## 2. Fórmula de cálculo

O frete especial é calculado como:

Valor do frete = Valor base × Multiplicador regional × Fator de peso

Onde:
- Valor base = tarifa publicada na tabela mensal de fretes.
- Multiplicador regional = fator aplicado conforme a região de destino (seção 2.1).
- Fator de peso = 1.0 para cargas de 500kg a 1.000kg; 1.2 para cargas de 1.001kg a 3.000kg; 1.5 para cargas acima de 3.000kg.

### 2.1. Multiplicadores regionais

| Região | Multiplicador |
|--------|--------------|
| Sul | 1.2 |
| Sudeste | 1.0 |
| Centro-Oeste | 1.3 |
| Nordeste | 1.4 |
| Norte | 1.6 |

## 3. Prazo de entrega para frete especial

O prazo de entrega para frete especial é calculado como o prazo padrão da rota + 2 dias úteis adicionais para manuseio de carga pesada.

## 4. Condições especiais

- Cargas acima de 5.000kg requerem aprovação prévia do gerente de operações regional.
- Cargas perigosas com peso acima de 500kg seguem tabela específica (PROC-043: Frete de Cargas Perigosas).
- Descontos de volume (mais de 10 fretes especiais/mês para o mesmo cliente) devem ser negociados pelo Comercial e registrados em aditivo contratual.

# PROC-042-v2 — Procedimento de Cálculo de Frete Especial (Revisado)

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

Durante o período de transição, chamados abertos antes de 01/12/2023 que ainda estejam em processamento devem usar os multiplicadores da versão anterior (PROC-042 v1). Chamados novos a partir de 01/12/2023 devem usar os multiplicadores desta versão.]
```

**Output obtido:**
```
Inconsistências identificadas entre PROC-042 v1.0 e v2.0
Item	v1.0 (03/03/2023)	v2.0 (10/11/2023)	Direção da mudança
Fator de peso — 1.001kg a 3.000kg	1,2	1,15	queda
Fator de peso — acima de 3.000kg	1,5	1,4	queda
Multiplicador regional — Sul	1,2	1,3	alta
Multiplicador regional — Sudeste	1,0	1,1	alta
Multiplicador regional — Centro-Oeste	1,3	1,4	alta
Multiplicador regional — Nordeste	1,4	1,5	alta
Multiplicador regional — Norte	1,6	1,8	alta
Prazo adicional para carga pesada	+2 dias úteis	+3 dias úteis	alta
Desconto por volume — gatilho	>10 fretes/mês, negociado caso a caso, via aditivo contratual	≥8 fretes/mês → 5% sobre o multiplicador regional; >15 fretes/mês → 10%	mudança de natureza (negociação → regra fixa), não só de número
Nota sobre PROC-043 (cargas perigosas)	Não existe	Adverte que a PROC-043 está em revisão pelo Compliance	só existe na v2
Regra de transição (seção 5)	Não existe	Chamados abertos antes de 01/12/2023 usam multiplicadores da v1; a partir dessa data, usam a v2	só existe na v2

Ficaram iguais entre as duas versões: a estrutura da fórmula (Valor base × Multiplicador regional × Fator de peso), as faixas de peso (500–1.000kg / 1.001–3.000kg / acima de 3.000kg — só os multiplicadores dentro delas mudaram), e a exigência de aprovação prévia para cargas acima de 5.000kg.

Risco de um assistente de IA misturar as duas versões

Eu classificaria esse risco como alto, e por motivos bem específicos ao conteúdo, não apenas porque "existem duas versões":

Os números são próximos o suficiente para não soarem um erro. 1,2 vs. 1,3, ou 1,4 vs. 1,5, são o tipo de diferença que passa despercebida numa resposta gerada — diferente de um erro grosseiro que o próprio texto entregaria como incoerente. Um assistente pode compor uma resposta plausível e internamente "sem contradições visíveis" mesmo pegando o multiplicador regional da v2 com o fator de peso da v1, por exemplo.

A direção da mudança não é uniforme. O multiplicador regional subiu na v2, mas o fator de peso caiu. Se o assistente usar uma heurística ingênua do tipo "a versão mais recente costuma ter valores mais altos, então valores mais altos = mais atual", ele vai acertar o multiplicador regional e errar o fator de peso (ou vice-versa) — produzindo um cálculo que nunca existiu em nenhuma das duas versões.

O desconto por volume não é só uma mudança de número, é uma mudança de mecanismo: de negociação caso a caso (v1) para regra percentual automática (v2). Misturar aqui é o pior cenário: por exemplo, aplicar o gatilho de 8 fretes/mês (v2) mas tratar como "precisa de aditivo contratual" (v1), ou aplicar desconto de 5% a partir de 10 fretes (mistura de limiar v1 com percentual v2) — nenhuma combinação híbrida corresponde a uma regra real da empresa.

O prazo de entrega (+2 vs. +3 dias) é uma promessa direta ao cliente. Um erro aqui vira risco de SLA descumprido ou de expectativa incorreta passada ao cliente — não é um erro só "interno".

O ponto mais crítico, porém, é a seção 5 da v2 (disposições transitórias). Ela informa que a resposta correta não depende apenas de "qual versão é a vigente", mas da data de abertura do chamado: chamados abertos antes de 01/12/2023 e ainda em processamento devem usar os multiplicadores da v1. Isso significa que mesmo um assistente que identificasse corretamente "a v2 é a mais nova, devo usar ela" ainda estaria errado para uma parcela dos casos reais — a v1 continua sendo a resposta certa em determinadas situações, indefinidamente, até que esses chamados antigos se encerrem (não há data-limite para isso). Nenhum dos dois documentos, isoladamente, resolve completamente a pergunta "qual multiplicador eu uso agora" sem essa terceira variável (data de abertura do chamado).

Some a isso o fato de que nenhum dos dois documentos declara formalmente que substitui o outro — ambos dizem textualmente que coexistem sem hierarquia clara no SharePoint. Isso é o cenário clássico de risco em base de conhecimento para IA (RAG): uma busca por "cálculo de frete especial" tende a recuperar as duas versões como igualmente relevantes (títulos quase idênticos, mesma estrutura, mesmo objetivo), e sem um sinal de metadado do tipo "vigente/obsoleto", o modelo não tem como discriminar sozinho — ele pode responder combinando trechos de ambas sem perceber, especialmente se a pergunta do usuário não mencionar a data do chamado.

Uma mitigação natural para quando vocês montarem o assistente de discovery/atendimento: tratar documentos conflitantes sem data de vigência explícita como uma condição bloqueante (o assistente deveria se recusar a responder com um número específico e sinalizar a ambiguidade, ou perguntar a data do chamado antes de calcular), em vez de silenciosamente escolher uma versão. Isso também é, em si, uma descoberta de processo: a NovaTech precisa de um dono formal de vigência documental antes de qualquer IA poder responder com segurança sobre esse cálculo.
```

Ver também: [etapa-2-analise-profunda.md](./etapa-2-analise-profunda.md)

## Etapa 3 — Cruzamento com o FAQ-Atendimento

**Prompt utilizado:**
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

**Output obtido:**
```
Cruzamento FAQ × documentos formais
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

```

Ver também: [etapa-3-cruzamento-faq.md](./etapa-3-cruzamento-faq.md)

## Mapa de riscos (mínimo 2)

# Mapa de Riscos — Discovery NovaTech (consolidado das Etapas 1-3)

| # | Risco | Impacto se não tratado | Como levar para o discovery |
|---|-------|--------------------------|------------------------------|
| 1 | PROC-042 existe em duas versões (v1.0 e v2.0) sem nenhuma marcada como obsoleta, coexistindo no SharePoint sem hierarquia — e a cláusula de transição da v2 (§5) já venceu há muito tempo: ela só cobria chamados abertos antes de 01/12/2023, e hoje estamos em 2026. | Um assistente (ou um atendente humano) pode citar multiplicador regional ou fator de peso de uma versão junto com o prazo de entrega da outra, gerando cobrança de frete incorreta e retrabalho de conciliação com o cliente. | Perguntar diretamente: "A cláusula de transição do §5 da v2 nunca foi encerrada formalmente — existe algum chamado residual ainda em processamento sob a regra da v1, ou essa cláusula já pode ser considerada extinta?" Propor que a resposta vire um metadado obrigatório de vigência no pipeline de ingestão, com a v1 arquivada com status explícito de "obsoleta desde [data]". |
| 2 | O item 45 do FAQ já mistura o limiar de volume da v1 (mais de 10 fretes/mês) com o mecanismo de desconto automático em tabela da v2 (a partir de 8 fretes/mês) — uma terceira regra, que não corresponde a nenhuma das duas versões oficiais, já em uso real pelos atendentes. | Desconto de frete concedido (ou negado) com base num critério que nunca foi aprovado formalmente — exposição financeira silenciosa, porque parece uma regra legítima e ninguém questiona. | Perguntar: "Quantos descontos de frete especial foram concedidos nos últimos 12 meses usando o critério do FAQ (>10 fretes, desconto automático), e existe orçamento/aprovação da Diretoria Comercial para isso retroativamente?" Propor auditoria pontual desses casos antes de formalizar qualquer regra nova. |
| 3 | O item 8 do FAQ instrui o atendente a checar a "data do contrato do cliente" para decidir qual tabela de frete usar — um critério diferente do oficial (data de abertura do chamado, conforme §5 da v2), criado informalmente para tapar a ambiguidade entre versões. | Aplicação sistematicamente errada da tabela de frete mesmo quando o atendente (ou o assistente) acredita estar seguindo uma regra válida — o erro é mais difícil de pegar porque parece justificado. | Perguntar: "Existe alguma política de 'tarifa travada por contrato' documentada no CRM ou em contrato-padrão? Se existir, como ela deveria se relacionar com a cláusula de transição por data de chamado da PROC-042 v2 — são a mesma regra ou duas coisas diferentes que estão sendo confundidas?" |
| 4 | Os itens 3 e 32 do FAQ mostram que exceções de carga perigosa (devolução, ou frete "expresso") são resolvidas informalmente via ramal da Gestão de Riscos ou aprovação ad hoc do Compliance (~2 dias) — sem nenhum documento do lote que defina quem tem autoridade para conceder essas exceções, nem que reconcilie o prazo real com a promessa comercial de "expresso". | Exposição regulatória/segurança se uma exceção informal for concedida indevidamente e comunicada ao cliente como regra padrão; e descumprimento sistemático da promessa de frete "expresso" para esse tipo de carga, sem que ninguém tenha medido isso formalmente. | Perguntar: "Quem tem autoridade formal para autorizar uma exceção de transporte/devolução de carga perigosa — isso está na PROC-043 (que sabemos estar em revisão pelo Compliance)? E o SLA de frete 'expresso' já contabiliza o prazo real de aprovação do Compliance, ou precisa ser renegociado com Operações?" |
| 5 | O item 38 do FAQ descreve um processo inteiro de sinistro por carga danificada (prazo de 48h para registro, laudo, reembolso integral via Jurídico) que não aparece coberto por nenhum dos 5 documentos formais do lote — inclusive não está claro se isso é parte do escopo de POL-001 (devolução) ou um processo totalmente à parte. | Se esse processo não estiver em nenhuma fonte normativa indexável, o assistente pode tratar carga danificada como uma devolução comum, fazendo o cliente perder o prazo de 48h ou pular a etapa de laudo — risco direto de o cliente perder o direito ao reembolso por orientação incorreta. | Perguntar: "O processo de sinistro por carga danificada está formalizado em algum documento fora do FAQ? Se não estiver, isso precisa virar uma política própria antes de entrar na base do assistente — proponho que a formalização desse processo seja um entregável específico da fase de discovery." |
| 6 | SLA-2024 tem dono compartilhado (Comercial + Operações), mas nenhum documento do lote amarra os prazos de resposta/resolução do SLA aos prazos reais das etapas operacionais que dependem de terceiros (aprovação de Compliance para carga perigosa, laudo do Jurídico para sinistro) — e o item 32 já mostra um caso concreto de descompasso (aprovação de ~2 dias para algo vendido como "expresso"). | O SLA promete prazos (ex.: Gold: 2h resposta / 24h resolução) que a operação não consegue cumprir de forma sistemática nos casos que dependem de aprovação externa — gerando cliente insatisfeito e possível penalidade contratual que a empresa nem sabe que está correndo. | Perguntar: "O SLA-2024 foi validado com Operações e Compliance antes de ser publicado? Existe hoje alguma medição real de aderência ao SLA separada por tipo de caso — especificamente para os que dependem de aprovação externa (carga perigosa, sinistro, frete especial)?" |

## Reflexão — progressivo vs. tudo de uma vez
 O que teria acontecido se você tivesse colado os 5 documentos completos
de uma vez no primeiro prompt? Compare com o resultado da abordagem progressiva.
Relacione com orçamento de atenção e context rot.

## Entregável

- [x] Estratégia de contexto documentada (acima)
- [x] 3 prompts com outputs (arquivos separados desta pasta)
- [ ] Análise crítica de cada etapa
- [ ] Reflexão sobre progressive disclosure
- [x] Mapa de riscos (mínimo 2 itens)
