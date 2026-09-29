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

**Autora:** Jaqueline Santos, Product Specialist
**Base:** discovery (exercício 1.1), jornada do atendente (exercício 1.2), Anexo A (documentos) e Anexo B (chunks, mapa de cobertura e armadilhas)
**Público:** time de produto, time técnico e QA. Cada requisito tem um critério de teste que pode ser executado sem conhecer a arquitetura interna.

### Contexto

Esta especificação transforma os riscos do discovery em requisitos para o pipeline de RAG:

- duas versões do PROC-042 coexistindo sem hierarquia;
- um FAQ informal, sem validação;
- documentos sem metadado de vigência;
- três áreas publicando todo mês sem um processo unificado de revisão.

### Princípios

1. **A vigência é decidida pelo dono do documento**, nunca deduzida pela data. Quando o documento define a própria regra de transição (PROC-042-v2 §5), o assistente aplica essa regra.
2. **Fonte oficial prevalece sobre fonte informal.** O FAQ nunca sustenta sozinho uma informação crítica.
3. **O assistente não completa lacunas.** Regras, prazos, valores, percentuais, contatos e procedimentos só aparecem se estiverem num trecho citado.
4. **Nada entra ou sai da base em silêncio.** Toda entrada, retirada, falha e conflito fica registrado.
5. **A base é curada continuamente.** Cada conflito, lacuna e feedback vai para uma fila com dono.

Convenções: **[PROPOSTA]** marca o que ainda precisa ser validado com a NovaTech. As armadilhas numeradas seguem o Anexo B.

---

### 1. Fontes de dados: o que indexar, excluir ou marcar como obsoleto

#### 1A. Decisão por fonte

| Fonte | Decisão | Motivo |
|---|---|---|
| POL-001 v3.1 (Operações) | Indexar como **normativo, vigente** | É a fonte oficial de devolução e avaria. |
| PROC-042-v2 v2.0 (Comercial) | Indexar como **normativo, vigente** para chamados a partir de 01/12/2023 (§5) | É a versão atual, e a própria §5 define a transição. |
| PROC-042 v1.0 (Comercial) | Indexar como **normativo, vigência residual** (só para chamados abertos antes de 01/12/2023 e ainda em processamento, conforme v2 §5) | Não pode sair da base sem decisão do dono, e a §5 ainda a referencia. |
| SLA-2024 v2024.1 (Comercial + Operações) | Indexar como **contratual, vigente** | Define tiers, prazos, incidente crítico e penalidades. |
| FAQ-Atendimento (sem dono formal) | Indexar como **informal, não validado**, com peso inferior às fontes oficiais | É útil para detectar divergências e lacunas, mas o próprio documento declara que não foi validado. |
| PROC-043 e PROC-088 | **Não disponíveis.** Registrar no catálogo como "documento citado, fora da base" | Permite ao assistente dizer qual documento contém a regra sem inventá-la (RF-SR-04). |
| Tabela mensal de fretes (planilha) | **[PROPOSTA]** Não indexar no MVP | O valor base muda todo mês, e o assistente não informa valor em R$ (RF-SR-01). Decisão a validar com o Comercial. |
| Confluence (~400 páginas, muda toda semana) | **[PROPOSTA]** Fora do MVP até haver inventário com dono e tipo por página | Sem dono e sem tipo, o volume multiplica os conflitos. |
| Outras planilhas | Fora da base até serem classificadas | Mesmo motivo. |

#### 1B. Requisitos

| # | Requisito | Critério de teste (QA) |
|---|---|---|
| RF-FON-01 | Todo documento indexado tem os metadados: ID, título, versão, data, status, tipo de fonte, área dona e relação com outras versões. **Status:** vigente, vigência residual, vigência não confirmada, em revisão ou obsoleto. **Tipo:** normativo, contratual ou informal. | Consultar os metadados de todos os documentos indexados. Nenhum campo pode estar vazio. |
| RF-FON-02 | Quando o documento não traz um metadado, a curadoria preenche um **valor padrão** antes da indexação: status "vigência não confirmada", tipo "não declarado" (tratado como **informal**) e dono "sem dono formal" (a curadoria assume). | PROC-042 v1, PROC-042-v2 e FAQ aparecem indexados, cada um com os campos preenchidos, e o FAQ aparece como informal e sem dono formal. |
| RF-FON-03 | O tipo de fonte define a prioridade: normativo e contratual vêm antes de informal na recuperação e na resposta. | Numa pergunta respondida pela POL-001 e pelo FAQ, a POL-001 é a fonte principal da resposta. |
| RF-FON-04 | Status e obsolescência só mudam com registro do dono. A data nunca é usada para deduzir vigência. | Tentar marcar um documento como obsoleto sem registro do dono. A operação fica pendente e não entra em vigor. |
| RF-FON-05 | Documento com falha de extração (por exemplo, PDF escaneado) ou sem metadados preenchidos não é indexado e aparece no **relatório de ingestão** (RF-ATU-06). | Enviar um PDF escaneado de teste. Ele não aparece como fonte e está listado no relatório como "falha de extração". |
| RF-FON-06 | Os documentos citados que estão fora da base ficam num catálogo com nome e documento de origem da citação. | Consultar o catálogo: PROC-043 (citado na PROC-042-v2 §4) e PROC-088 (citado na POL-001 §2) estão listados. |

---

### 2. Comportamento diante de documentos contraditórios

| # | Requisito | Critério de teste (QA) |
|---|---|---|
| RF-CON-01 | **Detectar conflito.** Quando os trechos recuperados vêm de versões diferentes do mesmo documento, ou de fontes que dizem coisas diferentes sobre o mesmo item, o assistente entra em modo conflito e avisa o atendente. | "Qual o multiplicador para o Sudeste?" A resposta indica que há duas versões: 1.0 (v1 §2.1) e 1.1 (v2 §2.1). |
| RF-CON-02 | **Aplicar a regra de transição quando o documento a define.** Para o PROC-042, o assistente pergunta a data de abertura do chamado se ela não foi informada. A partir de 01/12/2023, usa a v2. Antes dessa data e ainda em processamento, usa a v1 (v2 §5). Nunca usa "a versão mais recente" como critério. | (a) Sem data informada, o assistente pergunta a data antes de dar o multiplicador. (b) Com chamado de 2026, responde 1.1 e cita v2 §2.1 e §5. (c) Com chamado de 15/11/2023 ainda em processamento, responde 1.0 e cita v1 §2.1 e v2 §5. |
| RF-CON-03 | **Uma versão por cálculo.** Multiplicador, fator de peso, prazo adicional e desconto de um mesmo cálculo vêm da mesma versão, escolhida pelo RF-CON-02. Como a §5 fala só dos multiplicadores, o assistente avisa que os demais parâmetros seguem a mesma versão e que isso precisa de confirmação. **[PROPOSTA — validar com o Comercial]** | 2.000 kg para o Sudeste, chamado novo: a resposta usa 1.1 × 1.15 (tudo da v2) e nunca 1.1 × 1.2. Armadilha 1 do Anexo B. |
| RF-CON-04 | **Oficial prevalece sobre o FAQ.** Havendo divergência, a resposta começa pela regra oficial e cita o FAQ só como divergência rotulada "prática informal, não validada". | (a) Desconto de volume: responde com a v2 §4 (5% a partir de 8 fretes/mês) e cita o FAQ-45 como divergência. (b) Tabela de frete para contrato antigo: responde com a v2 §5 (data do chamado) e cita o FAQ-8 (data do contrato) como divergência. (c) Carga avariada: responde com a POL-001 §3.5 e cita o FAQ-38 como divergência. |
| RF-CON-05 | **Ambiguidade dentro de um documento.** Quando o texto oficial permite mais de uma leitura, o assistente não calcula e encaminha ao dono. Caso conhecido: "5% sobre o multiplicador regional" (v2 §4) pode dar 1,235 ou 1,25. | "Qual o desconto para 9 fretes/mês no Sul?" O assistente informa a regra da v2 §4, não informa o multiplicador final e encaminha ao Comercial. |
| RF-CON-06 | **Todo conflito vira registro.** Cada conflito detectado vai para a fila de curadoria (seção 6) com a pergunta, as fontes envolvidas e os trechos. | Depois de rodar o conjunto de aceite (seção 7), o registro contém: PROC-042 v1 × v2, FAQ-45 × v2 §4, FAQ-8 × v2 §5 e FAQ-38 × POL-001 §3.5. |
| RF-CON-07 | **Conflito entre normativo e contratual.** Enquanto a hierarquia não for definida (pergunta em aberto), o assistente mostra as duas fontes lado a lado e recomenda escalar. | Caso sintético de teste: um documento normativo e um contratual com prazos diferentes para o mesmo item. A resposta mostra os dois e não escolhe. |

---

### 3. Comportamento quando a pergunta não tem resposta na base

| # | Requisito | Critério de teste (QA) |
|---|---|---|
| RF-SR-01 | **Limite do conhecimento geral.** O modelo pode usar conhecimento geral para entender a pergunta e redigir a resposta, mas nunca para regras, prazos, valores, percentuais, contatos ou procedimentos da NovaTech. Toda inferência que não está num trecho é declarada. | "Frete para 600 kg para Manaus": a resposta diz "considerando Manaus na região Norte" para o atendente confirmar e não informa valor em R$ (o valor base não está na base). |
| RF-SR-02 | **Resposta padrão de "não encontrei".** Contém: (a) a frase explícita de que não encontrou; (b) o documento mais próximo, se houver, e por que não se aplica; (c) a sugestão de escalar ao supervisor. | "Frete para 300 kg para Salvador?" A resposta tem os três elementos e nenhum valor. |
| RF-SR-03 | **Cobertura parcial conta como "não encontrado".** Um trecho parecido, mas fora do escopo que o próprio documento declara, não autoriza resposta. | Nenhum multiplicador da PROC-042 aparece em resposta sobre carga de até 500 kg (v2 §1). Armadilha 5 do Anexo B. |
| RF-SR-04 | **Documento citado, mas fora da base.** O assistente informa o nome do documento, diz que ele não está disponível e não aplica regras gerais no lugar dele. | Frete de carga perigosa de 800 kg: a resposta não aplica os multiplicadores gerais e cita a PROC-043 como fora da base. |
| RF-SR-05 | **Premissa falsa é corrigida, não respondida.** Se a pergunta traz algo que a base desmente, o assistente corrige com a fonte. | "Qual o SLA do cliente Platinum?" A resposta cita a SLA-2024 §1 (só existem Gold, Silver e Standard) e não atribui SLA a esse tier. Armadilha 3 do Anexo B. |
| RF-SR-06 | **Só existe o FAQ.** O assistente diz que não há documento oficial e mostra o FAQ como "prática relatada, não validada". Em tema crítico (carga perigosa, avaria ou sinistro, valores, percentuais, prazos, contatos), o conteúdo do FAQ nunca é apresentado como resposta ao cliente, e o assistente recomenda escalar. | (a) "Posso enviar carga perigosa com frete expresso?" A resposta abre com "não há procedimento oficial", cita o FAQ-32 como informal e recomenda escalar. (b) "Qual o percentual do seguro de carga?" Mesmo comportamento com o FAQ-22. |
| RF-SR-07 | **Exceção nunca vira permissão.** Quando o documento diz que algo não é elegível, a resposta começa pelo "não" e depois indica o destino. Exceções relatadas no FAQ não são prometidas. | "Posso devolver carga perigosa?" A resposta começa com "Não pelo processo padrão" e cita a POL-001 §3.2. Não aparece "sim" nem prazo de devolução. |
| RF-SR-08 | **Sem recusa indevida.** Se existe um trecho que responde a pergunta, o assistente responde. | Rodar as perguntas do mapa de cobertura do Anexo B que têm trecho associado: nenhuma pode retornar "não encontrei". **[PROPOSTA]** Meta: 0 recusas indevidas no conjunto de regressão. |
| RF-SR-09 | **Todo "não encontrei" é registrado** na fila de curadoria, com a pergunta e os trechos mais próximos. | Depois de rodar o conjunto de aceite, o registro contém as perguntas de RF-SR-02 e RF-SR-04. |

---

### 4. Requisitos de atualização

| # | Requisito | Critério de teste (QA) |
|---|---|---|
| RF-ATU-01 | **Ingestão automática com prazo.** Documento publicado numa fonte indexada fica disponível como fonte citável em até **[PROPOSTA: 24h úteis]**, contadas a partir da publicação na fonte. | Publicar um documento de teste e medir o tempo até ele ser citado. Falha se passar do prazo. |
| RF-ATU-02 | **Nova versão.** Quando um documento ganha nova versão, o status da anterior é atualizado conforme a decisão do dono (RF-FON-04), e ela deixa de ser fonte principal no mesmo prazo do RF-ATU-01. | Publicar a v2 de um documento de teste. Depois do prazo, a v2 é a fonte principal e a v1 aparece só com o status definido. |
| RF-ATU-03 | **Retirada.** Documento retirado da fonte sai da busca no mesmo prazo do RF-ATU-01. Se a retirada não tiver registro do dono, o documento sai da busca do mesmo jeito e gera um alerta para a curadoria. | Retirar um documento de teste da fonte. Depois do prazo, ele não é mais citado, e o alerta aparece se não houver registro do dono. |
| RF-ATU-04 | **Mesmo prazo para todas as áreas.** Operações, Compliance e Comercial têm o mesmo prazo de ingestão. | Publicar documentos de teste das três áreas. Todos ficam disponíveis dentro do prazo. |
| RF-ATU-05 | **Checagem de conflito na ingestão.** Todo documento novo é comparado com os documentos já indexados do mesmo tema. Se houver conflito, gera um registro (RF-CON-06) antes de ser publicado para o assistente. **[PROPOSTA]** | Publicar um documento de teste com multiplicador diferente da v2. O conflito é registrado antes de o documento ser citado. |
| RF-ATU-06 | **Relatório de ingestão.** Cada ciclo de ingestão gera um relatório com o que entrou, o que saiu, o que falhou (e por quê) e os conflitos detectados. | Depois dos testes RF-ATU-01 a RF-ATU-05 e RF-FON-05, o relatório lista cada evento. |

---

### 5. Requisitos de rastreabilidade

| # | Requisito | Critério de teste (QA) |
|---|---|---|
| RF-RAS-01 | **Citação completa.** Toda resposta com regra, prazo, valor ou contato cita documento, versão, data, seção e tipo de fonte. | Amostrar respostas dos 4 temas do discovery (prazo, frete, devolução, outros). 100% trazem os cinco campos. |
| RF-RAS-02 | **Trecho literal.** O trecho de onde veio a informação é exibido literalmente junto com a resposta, sem resumo. | Comparar o trecho exibido com o documento de origem. O texto é idêntico. |
| RF-RAS-03 | **Todo número está no trecho.** Todo prazo, valor, percentual e contato da resposta aparece no trecho citado. | Verificação automática: extrair números e contatos de cada resposta do conjunto de aceite e procurá-los nos trechos citados. Nenhum pode ficar sem correspondência. |
| RF-RAS-04 | **Uma citação por fonte.** Quando a resposta usa mais de uma fonte, cada uma é citada separadamente, com a sua versão. | No caso do Sudeste (RF-CON-01), aparecem duas citações: v1 §2.1 e v2 §2.1. |
| RF-RAS-05 | **Sem fonte, sem resposta.** Se a resposta não puder ser associada a um trecho, o assistente segue o RF-SR-02. | Auditar uma amostra. Nenhuma resposta com regra aparece sem citação. |
| RF-RAS-06 | **Registro de cada resposta** com pergunta, trechos recuperados, fontes citadas e versão do prompt, para auditoria e triagem de feedback. **[PROPOSTA]** | Escolher uma resposta qualquer do conjunto de aceite e recuperar esses quatro dados no registro. |

---

### 6. Curadoria da base

| # | Requisito | Critério de teste (QA) |
|---|---|---|
| RF-CUR-01 | **Existe um papel de curadoria** responsável por: preencher metadados padrão (RF-FON-02), triar a fila e acompanhar as decisões dos donos. **[PROPOSTA — responsável a definir]** | O papel está nomeado antes do go-live. |
| RF-CUR-02 | **Fila única de curadoria**, que recebe conflitos (RF-CON-06), "não encontrei" (RF-SR-09), falhas de ingestão (RF-ATU-06) e feedback do atendente (jornada 1.2, R1). | Cada tipo de evento do conjunto de aceite aparece na fila com a sua origem. |
| RF-CUR-03 | **Dono por documento**, conforme o cabeçalho: POL-001 → Operações; PROC-042 e PROC-042-v2 → Comercial; SLA-2024 → Comercial + Operações; PROC-043 → Compliance (em revisão, conforme v2 §4); FAQ → sem dono formal (a curadoria responde até haver dono). | Os metadados de cada documento mostram o dono indicado. |
| RF-CUR-04 | **Triagem por causa:** documento (vai para a área dona), busca (vai para o time técnico) ou prompt (vai para o time técnico). Uma correção só é fechada depois de refazer a pergunta original e o conjunto de regressão. | Uma correção de teste só muda para "fechado" depois de registrado o teste de regressão aprovado. |
| RF-CUR-05 | **Decisões que precisam existir antes do go-live** **[PROPOSTA]**: (1) status da PROC-042 v1 (arquivar ou manter como residual); (2) fronteira entre avaria (POL-001 §3.5) e sinistro (FAQ-38); (3) se o critério do FAQ-45 está sendo aplicado hoje; (4) hierarquia entre normativo e contratual; (5) se a tabela mensal de fretes entra na base; (6) escopo do Confluence. | Checklist de go-live com as seis decisões registradas e assinadas pelo dono de cada uma. |

---

### 7. Conjunto de aceite

| Pergunta | Comportamento esperado | Requisitos |
|---|---|---|
| "Qual o multiplicador para o Sudeste?" (sem data) | Informa que há duas versões e pergunta a data do chamado | RF-CON-01, RF-CON-02 |
| O mesmo, com chamado aberto em 2026 | 1.1, citando v2 §2.1 e §5 | RF-CON-02, RF-RAS-01 |
| "2.000 kg para o Sudeste, chamado novo" | 1.1 × 1.15, tudo da v2, com aviso sobre o alcance da §5; sem valor em R$ | RF-CON-03, RF-SR-01 |
| "Desconto para 9 fretes/mês?" | Regra da v2 §4; FAQ-45 como divergência; não calcula; encaminha ao Comercial | RF-CON-04, RF-CON-05 |
| "Carga chegou avariada, o que faço?" | Responde com a POL-001 §3.5; FAQ-38 como divergência | RF-CON-04 |
| "Frete para 300 kg para Salvador?" | "Não encontrei", documento mais próximo, escalar | RF-SR-02, RF-SR-03 |
| "Frete de carga perigosa de 800 kg" | Cita a PROC-043 como fora da base; não aplica multiplicadores | RF-SR-04 |
| "SLA do cliente Platinum?" | Corrige a premissa citando a SLA-2024 §1 | RF-SR-05 |
| "Carga perigosa com frete expresso?" | Sem procedimento oficial; FAQ-32 como informal; escalar | RF-SR-06 |
| "Posso devolver carga perigosa?" | Começa com "Não pelo processo padrão", cita a POL-001 §3.2 | RF-SR-07 |
| Perguntas do mapa de cobertura com trecho | Todas respondidas com citação | RF-SR-08, RF-RAS-01 a 03 |

---

### 8. Pendências para validar antes do desenvolvimento

1. Prazo de ingestão (RF-ATU-01). A proposta é 24h úteis.
2. Se os demais parâmetros do cálculo seguem a versão indicada pela §5 (RF-CON-03).
3. Hierarquia entre documento normativo e contratual (RF-CON-07).
4. Responsável pela curadoria e cadência de revisão da fila (RF-CUR-01).
5. As seis decisões de go-live (RF-CUR-05).
6. Meta de recusas indevidas (RF-SR-08).

---

## Análise do feedback do Claude

| Ponto do feedback | Decisão | Como entrou na v3 |
|---|---|---|
| O item 1 excluía os documentos que o item 2 usa | Aceito | RF-FON-02: metadados padrão preenchidos pela curadoria. O FAQ entra como informal, sem dono formal. |
| Faltava decidir quais fontes indexar | Aceito, com adaptação | Tabela 1A. Adaptei a sugestão: em vez de indexar tudo, deixei a tabela mensal de fretes e o Confluence fora do MVP, como proposta. |
| Atualização sem requisito | Aceito | Seção 4, com prazo proposto, início do relógio na publicação, retirada, checagem de conflito e relatório. |
| Curadoria quase ausente | Aceito | Nova seção 6, com fila única, dono por documento, triagem por causa e decisões de go-live. |
| Regra de transição não especificada | Aceito | RF-CON-02 (perguntar a data) e RF-CON-03 (o que a §5 não cobre). |
| Só FAQ em tema crítico | Aceito | RF-SR-06. |
| Recusa indevida e registro de "não encontrei" | Aceito | RF-SR-08 e RF-SR-09. |
| Rastreabilidade incompleta | Aceito | RF-RAS-02 (trecho literal, sem resumo) e RF-RAS-03 (todo número no trecho). |
| Referências soltas e numeração | Aceito | "Relatório de ingestão" definido (RF-ATU-06), códigos por seção e o conjunto de aceite passou a existir (seção 7). |
| Tipo "não declarado" como valor padrão | Adaptado | Documento de tipo não declarado é tratado como **informal**, que é a opção mais conservadora. |
---

## Pendências para validar antes do desenvolvimento

O valor do prazo de ingestão (RF4.1/RF4.2) ainda não tem número confirmado pela NovaTech — está proposto como baseline de 24h úteis. A definição de quem tem autoridade formal para marcar um documento como obsoleto (RF1.5) também depende de uma decisão de governança que ainda não foi validada com Operações/Comercial/Compliance.

## Entregável

- [ ] Especificação final (v2)
- [ ] Histórico de iteração (v1 → feedback do Claude → v2)

