# Exercício 1.2 — Design de jornada com componente de IA

**Papel:** Product Specialist (Analista de Requisitos)
**Ferramentas utilizadas:** Claude (chat) + Claude Design

**Enunciado**
1. Usando o **Claude**, elabore a jornada do atendente em formato de texto estruturado, incluindo:
   - O fluxo principal: atendente recebe dúvida → consulta o assistente → recebe resposta com fonte → usa no atendimento.
   - O fluxo de fallback: o que acontece quando o assistente não tem confiança na resposta ou quando o atendente discorda.
   - O fluxo de feedback: como o atendente sinaliza que uma resposta estava errada, desatualizada ou incompleta.
   - Ao menos 2 guardrails de comportamento do assistente (ex: "nunca inventar um prazo que não esteja documentado").
  
2. Usando o **Claude Design**, transforme a jornada textual em um diagrama visual de fluxo que mostre os 3 caminhos (principal, fallback, feedback) de forma clara para apresentar ao time e ao cliente.
 
**Contexto**

Mapeei a jornada do atendente da NovaTech usando o assistente de IA, partindo dos dados do discovery (simulado) e dos riscos documentais levantados no exercício 1.1. Minha premissa de desenho é que o assistente nunca substitui a decisão humana quando as fontes são contraditórias, ausentes ou informais, e que toda correção feita por pessoas precisa voltar para a base que o assistente consulta.

Processo adotado
V1 (Claude): pedi a jornada completa e uma autocrítica do próprio modelo.
Análise crítica minha: revisei a V1 contra as conclusões do 1.1 e contra a autocrítica do Claude, e decidi o que aceitar, corrigir ou deixar como pergunta aberta.
V2 (refinada por mim): reescrevi a jornada incorporando essas decisões.
Diagrama (Claude Design): gerei o diagrama a partir da V2 e conferi cada exemplo contra o corpus antes de considerá-lo pronto.

**Prompt utilizado no Claude:**
```
# Contexto
Sou Product Specialist da DB1 no projeto da NovaTech: um assistente de IA com RAG para o time de atendimento. A NovaTech é uma empresa de logística. O time de atendimento tem 45 pessoas, recebe 320 chamados por dia e cerca de 60% deles exigem consulta à documentação. Hoje a busca leva em média 12 minutos por chamado, e a diretoria quer reduzir para menos de 2 minutos. O assistente vai funcionar no Teams + SharePoint e responder com base na documentação oficial, sempre indicando a fonte.

Dados do discovery:
- Os atendentes abrem em média 4 fontes diferentes por chamado.
- Dúvidas mais comuns: prazos de entrega (35%), regras de frete (25%), política de devolução (20%) e outros (20%).
- Em 15% dos casos, o atendente não encontra a resposta e escala para o supervisor.
- A documentação é atualizada todo mês por 3 áreas (Operações, Compliance, Comercial), sem um processo unificado de revisão. Alguns documentos se contradizem entre versões, e hoje o time resolve isso "perguntando para quem sabe".

Guardrails já definidos para o assistente (ponto de partida):
1. Sempre citar a fonte do documento.
2. Nunca inventar prazos ou valores que não estejam na documentação.
3. Quando não encontrar a resposta, dizer explicitamente que não encontrou e sugerir escalar para o supervisor.
4. Responder em português formal, mas acessível.

Anexei:
- Anexo A: os 5 documentos da NovaTech (POL-001, PROC-042, PROC-042-v2, SLA-2024, FAQ-Atendimento), com as contradições e gaps já mapeados.
- Anexo B: os chunks do pipeline de RAG, o mapa de cobertura (pergunta → chunks) e as armadilhas conhecidas.

# Tarefa
Elabore a jornada do atendente usando o assistente, em texto estruturado, com 4 seções:

1. **Fluxo principal (caminho feliz):** o atendente recebe a dúvida → consulta o assistente → recebe a resposta com a fonte → usa no atendimento. Para cada etapa, informe: quem age, o que faz, o que o assistente entrega (formato da resposta: conteúdo, documento, versão e seção citados) e o que o atendente verifica antes de repassar ao cliente.

2. **Fluxo de fallback:** cubra pelo menos estas situações:
   a) O assistente não encontra resposta na base (ex.: frete abaixo de 500kg, que não tem documento).
   b) O assistente encontra fontes contraditórias (ex.: PROC-042 v1 vs v2, incluindo a regra de transição da seção 5 da v2).
   c) A única fonte disponível é o FAQ informal, sem validação (ex.: FAQ-32 carga perigosa com frete expresso, FAQ-38 carga danificada).
   d) O atendente discorda da resposta do assistente.
   Para cada situação, descreva o comportamento do assistente, a ação do atendente e o destino da escalação (supervisor, ou a área/contato que o documento indicar).

3. **Fluxo de feedback:** como o atendente sinaliza que uma resposta estava errada, desatualizada ou incompleta; que informações o feedback deve registrar (pergunta, resposta, fonte citada, tipo de problema, correção sugerida); e como esse feedback volta para a manutenção da base (curadoria dos documentos, ajuste do retrieval ou do prompt). Deixe claro que o RAG precisa de manutenção contínua e mostre o ciclo fechado: sinalização → triagem → correção → validação → atualização da base.

4. **Guardrails de comportamento:** pelo menos 4 guardrails específicos do domínio de logística/atendimento da NovaTech, cada um com: a regra, o motivo (qual risco evita) e um exemplo concreto tirado dos Anexos A/B. Aproveite as armadilhas do Anexo B, como tier "Platinum" inexistente, inversão da regra de carga perigosa, mistura de multiplicadores entre versões, uso do FAQ como fonte para informação crítica e pergunta sem cobertura.

# Regras
- Use **somente** as informações do contexto acima e dos anexos. Não invente prazos, valores, ramais, e-mails, áreas, cargos ou sistemas.
- Quando precisar propor algo que não está documentado (ex.: botão de feedback no Teams, nível de confiança, responsável pela triagem), marque explicitamente como **[PROPOSTA — validar no discovery]**.
- Ao citar uma regra da NovaTech, indique o documento e a seção (ex.: POL-001, seção 3.2).
- Se faltar informação para alguma parte da jornada, liste como pergunta em aberto em vez de supor.

# Formato de saída
- Título e uma breve descrição dos atores (atendente, assistente, supervisor, áreas envolvidas).
- As 4 seções acima, com etapas numeradas e pontos de decisão explícitos (ex.: "SE o assistente não encontrar fonte → ir para Fallback A").
- Ao final: uma lista de **perguntas em aberto** para validar com a NovaTech e uma lista dos itens marcados como **[PROPOSTA]**.
- Escreva de forma que a jornada possa ser convertida depois em um diagrama de fluxo com 3 caminhos (principal, fallback e feedback).

```
### Output obtido (V1):

```
 O atendente consulta o assistente no Teams, recebe uma resposta com documento, versão e seção, confere e só então repassa ao cliente. Quando a base não cobre, se contradiz ou só tem o FAQ informal, o assistente diz isso e indica o destino da escalação. Cada problema sinalizado alimenta um ciclo de manutenção da base.
Meta de referência: reduzir a busca de 12 para menos de 2 minutos por chamado, nos cerca de 60% dos 320 chamados/dia que exigem documentação. Hoje o atendente abre em média 4 fontes por chamado e escala ao supervisor em 15% dos casos.

Convenções para o diagrama
• Cada etapa tem um código para virar um nó: P (principal), FA a FE (fallbacks), R (feedback).
• Pontos de decisão aparecem como ◇ SE … → ir para … e viram losangos.
• Os três caminhos se cruzam em pontos fixos: P4 desvia para os fallbacks, e P7, FA a FE desembocam em R1.

**1. Fluxo principal (caminho feliz)**
No caminho feliz, a base tem um documento normativo ou contratual, sem conflito de versão, que responde à pergunta. O atendente só repassa ao cliente depois de conferir a fonte e as condições do caso.
P1. Recebe a dúvida
• Quem age: atendente.
• O que faz: lê o chamado e separa os dados que mudam a resposta: tier do cliente, número do CT-e, data de abertura do chamado, peso, região de destino e tipo de carga (perigosa, refrigerada, lacrada).
• ◇ SE a dúvida não exige documentação (cerca de 40% dos chamados) → atendimento normal, fora desta jornada.
P2. Consulta o assistente
• Quem age: atendente.
• O que faz: pergunta ao assistente, em linguagem natural, já incluindo os dados de P1. Exemplo: "Frete especial para 600 kg, destino Manaus, chamado aberto hoje".
P3. Busca e checagem da base
• Quem age: assistente.
• O que faz: recupera os trechos mais relevantes (tipicamente 3 a 5, conforme o Anexo B) e checa quatro coisas antes de responder.
• ◇ SE nenhum trecho cobre a pergunta → Fallback A.
• ◇ SE há trechos de versões que se contradizem → Fallback B.
• ◇ SE a única fonte é o FAQ-Atendimento → Fallback C.
• ◇ SE o documento remete a outro que não está na base (PROC-043, PROC-088) → Fallback E.
• ◇ SE a pergunta traz uma premissa que a base desmente (ex.: tier Platinum) → segue para P4 corrigindo a premissa (Guardrail G1).
• SENÃO → P4.
P4. Entrega a resposta com fonte
• Quem age: assistente.
• O que entrega, sempre nesta ordem:
    1. Resposta direta, em 1 a 3 frases.
    2. Condições e exceções que mudam a resposta.
    3. Fonte: documento · versão · data · seção · trecho literal.
    4. Tipo de fonte: normativo, contratual ou informal (conforme a classificação no cabeçalho de cada documento).
    5. Alertas: dado faltante, versão, fonte informal.
    6. Próximo passo ou destino de encaminhamento indicado pelo documento.
    7. Nível de confiança — [PROPOSTA — validar no discovery].
• Exemplo (devolução): "O cliente pode solicitar a devolução em até 7 dias úteis após o recebimento confirmado no tracking, sem contar sábados, domingos e feriados nacionais. Não se aplica a carga perigosa (classes 1 a 6 da ANTT), refrigerada com cadeia de frio rompida ou com lacre violado sem registro; nesses casos, o cliente deve procurar a Gestão de Riscos, ramal 4500. Fonte: POL-001, v3.1, 15/01/2024, seções 3.1 e 3.2 (normativo)."
• Exemplo (SLA): "Cliente Gold: primeira resposta em até 2h úteis e resolução em até 24h úteis em chamados gerais. Em incidente crítico: 30 min e 4h. Fonte: SLA-2024, v2024.1, 02/01/2024, seção 2 (contratual)."
P5. Verifica antes de repassar
• Quem age: atendente.
• O que verifica:
    ◦ A fonte é normativa ou contratual, e não o FAQ.
    ◦ O caso cabe nas condições: peso acima de 500 kg para frete especial (PROC-042-v2, seção 1), carga fora das exceções da POL-001, seção 3.2.
    ◦ O tier informado pelo cliente confere com o contrato (SLA-2024, seção 1).
    ◦ O caso não é incidente crítico (SLA-2024, seção 3), que tem prazos próprios.
    ◦ Não há valor final em R$ sem o valor base da tabela mensal, que não está na base.
• ◇ SE tudo confere → P6.
• ◇ SE o atendente discorda da resposta → Fallback D.
• ◇ SE a dúvida é sobre a aplicação ao caso concreto → supervisor.
P6. Usa no atendimento
• Quem age: atendente.
• O que faz: responde ao cliente com as próprias palavras, sem prometer nada além do trecho citado.
P7. Avalia a resposta
• Quem age: atendente.
• O que faz: marca a resposta como útil ou com problema. [PROPOSTA — validar no discovery]: botões no card do Teams.
• ◇ SE houve problema → R1 (Fluxo de feedback). SENÃO → fim.

**2. Fluxos de fallback**
Em todo fallback o assistente diz o que encontrou, o que não encontrou e para onde encaminhar. Ele nunca preenche a lacuna com suposição. Todos os fallbacks terminam em R1, para que o problema chegue à manutenção da base.

Fallback A — Sem resposta na base
• Gatilho: quando não há documentação válida sobre o tema na base. Exemplo do Anexo B: "Frete para 300 kg para Salvador?".
• FA1. Assistente: diz explicitamente que não encontrou. Explica o limite do que achou: a PROC-042-v2 cobre apenas cargas acima de 500 kg (seção 1). Não aplica os multiplicadores do frete especial a 300 kg e não estima valor.
• FA2. Resposta-modelo: "Não encontrei na documentação disponível a regra de frete para cargas até 500 kg. A PROC-042-v2, seção 1, trata apenas de frete especial acima de 500 kg. Recomendo escalar ao supervisor."
• FA3. Atendente: não responde valor ao cliente. Informa que vai confirmar e escala.
• Destino: supervisor (guardrail 3). A área dona do frete padrão não está documentada (pergunta em aberto).
• Mesmo tratamento para: o prazo padrão da rota. A PROC-042 e a v2 (seção 3) somam dias a esse prazo, mas ele não está na base. Isso afeta a categoria mais frequente de dúvida (prazos de entrega, 35%).

Fallback B — Fontes contraditórias (PROC-042 v1 × v2)
• Gatilho: a busca traz trechos das duas versões. Exemplos do Anexo B: "Frete para 600 kg para Manaus?" e "Qual o multiplicador para o Sudeste?".
• FB1. Assistente identifica o conflito e não mistura as versões. Os cabeçalhos das duas dizem que coexistem sem hierarquia formal.
• FB2. Aplica a regra de transição da PROC-042-v2, seção 5, que depende da data do chamado:
    ◦ ◇ SE a data não foi informada → pergunta: "O chamado foi aberto antes de 01/12/2023 e ainda está em processamento?"
    ◦ ◇ SE o chamado é de 01/12/2023 em diante → multiplicadores da v2 (ex.: Norte 1.8; Sudeste 1.1).
    ◦ ◇ SE foi aberto antes e segue em processamento → multiplicadores da v1 (ex.: Norte 1.6; Sudeste 1.0).
• FB3. Sinaliza o que a regra não resolve. A seção 5 fala só de multiplicadores. Também diferem entre as versões:
    ◦ fator de peso: 1.2/1.5 na v1 e 1.15/1.4 na v2 (seção 2);
    ◦ prazo adicional: +2 dias úteis na v1 e +3 na v2 (seção 3);
    ◦ desconto de volume: negociado acima de 10 fretes/mês na v1; 5% a partir de 8 e 10% acima de 15 na v2 (seção 4).
    ◦ Para esses itens, o assistente mostra os dois valores lado a lado, cada um com a sua fonte, e indica qual é a versão mais recente.
• FB4. Resposta-modelo (600 kg, Norte, chamado novo): "Valor do frete = valor base × 1.8 (Norte) × 1.0 (fator de peso de 500 a 1.000 kg). Fonte: PROC-042-v2, v2.0, 10/11/2023, seções 2, 2.1 e 5. O valor base está na tabela mensal de fretes, que não está na base, por isso não informo valor em R$. Atenção: a PROC-042 v1 continua publicada com multiplicador 1.6 para o Norte. Prazo: prazo padrão da rota + 3 dias úteis (v2) ou + 2 (v1); a regra de transição não trata do prazo."
• FB5. Atendente: confere a data do chamado e a região assumida (ex.: Manaus = Norte). Repassa apenas o que a regra de transição resolve. O que ficar em aberto (prazo, fator de peso), escala.
• Destino: supervisor, para o atendimento. A contradição em si vai para o Comercial, dono da PROC-042 e da v2, via R1.
• Efeito em cadeia: o frete reverso por desistência usa "os mesmos multiplicadores do frete original" (POL-001, seção 3.5). Por isso, a versão aplicada ao frete original também importa na devolução.

Fallback C — Só existe o FAQ informal
• Gatilho: a única fonte recuperada é o FAQ-Atendimento. O próprio documento diz que não foi validado por Compliance ou Operações e pede para confirmar informações críticas na documentação normativa.
• FC1. Assistente: abre dizendo que não há documento oficial sobre o tema. Mostra o conteúdo do FAQ como "prática relatada pelo time, não validada", nunca como regra. Não apresenta prazos, percentuais ou contatos do FAQ como compromisso com o cliente.
• FC2. Exemplo FAQ-32 (carga perigosa com frete expresso): "Não há procedimento oficial na base. O FAQ, item 32 (informal), relata que seria preciso autorização do Compliance e documentação ANTT atualizada, com cerca de 2 dias para autorizar. Não confirme isso ao cliente sem validação." Alerta adicional: carga perigosa com irregularidade de documentação ou rastreamento é incidente crítico (SLA-2024, seção 3).
• FC3. Exemplo FAQ-38 (carga danificada): o assistente mostra primeiro o que é oficial. A POL-001, seção 3.5, trata avaria em trânsito como devolução sem custo para o cliente, no prazo de 7 dias úteis da seção 3.1. Em seguida, sinaliza que o FAQ-38 descreve outro processo (registro em 48h, Jurídico, sinistros@novatech.com.br), sem respaldo formal. Os dois podem estar em conflito.
• FC4. Atendente: não repassa o conteúdo do FAQ como regra. Diz ao cliente que vai confirmar o procedimento e escala.
• Destino: supervisor. O FAQ indica Compliance (item 32) e Jurídico/sinistros (item 38), mas esses destinos são informais. Quem valida e se eles valem é pergunta em aberto.
• Saída: R1, tipo "fonte não oficial", para a curadoria decidir se o tema vira documento formal (dono provável: Compliance para o item 32; Operações, dona da POL-001, para o item 38).
Fallback D — Atendente discorda da resposta
• Gatilho: o atendente acha que a resposta está errada, desatualizada ou incompleta. Exemplo: a resposta cita desconto de 5% a partir de 8 fretes/mês (PROC-042-v2, seção 4), mas o atendente conhece a regra do FAQ-45 (desconto automático acima de 10 fretes).
• FD1. Atendente: não usa a resposta contestada com o cliente. Pede ao assistente o trecho literal e as outras fontes encontradas.
• FD2. Assistente: mostra o trecho literal e as fontes alternativas recuperadas, com tipo de fonte e data. Mantém a resposta se ela está no documento oficial; só muda diante de outra fonte da base. Se o atendente citar um documento fora da base, o assistente diz que não consegue verificá-lo.
• ◇ SE o trecho resolve a dúvida → volta a P5.
• ◇ SE a divergência continua → supervisor decide o atendimento e o atendente abre R1, tipo "discordância", indicando a fonte que considera correta.
• Destino: supervisor, imediato. Depois, curadoria e área dona do documento via R1.
Fallback E — Documento remete a outro fora da base
• Gatilho: a regra está num documento citado, mas não indexado. Exemplos: carga perigosa acima de 500 kg segue a PROC-043 (PROC-042-v2, seção 4, que avisa que ela está em revisão pelo Compliance). Mercadoria em trânsito segue a PROC-088 (POL-001, seção 2).
• FE1. Assistente: informa que a regra está no documento X, que não está disponível. Não aplica a tabela geral da PROC-042 a carga perigosa.
• FE2. Atendente: escala sem responder valor ou prazo.
• Destino: supervisor. Saída: R1, tipo "documento fora da base", para avaliar a indexação.

3. Fluxo de feedback
O RAG só é tão bom quanto a base. Três áreas atualizam a documentação todo mês, sem revisão unificada, então cada publicação pode criar uma nova contradição, como a PROC-042 v1 × v2. O ciclo abaixo transforma cada problema sinalizado numa correção verificada: sinalização → triagem → correção → validação → atualização da base → retorno ao atendente.

R1. Sinalização
• Quem age: atendente (manual) ou assistente (automático).
• Manual: a partir de P7 ou de qualquer fallback, o atendente marca a resposta como errada, desatualizada ou incompleta. [PROPOSTA — validar no discovery]: botão "Reportar problema" no card do Teams, com formulário curto.
• Automático [PROPOSTA — validar no discovery]: o assistente registra sozinho toda resposta "não encontrei" (FA), todo conflito de versões (FB), toda resposta só com FAQ (FC) e toda remissão a documento fora da base.

R2. Triagem
• Quem age: curadoria da base. [PROPOSTA — validar no discovery]: papel e responsável não existem hoje.
• O que faz: confirma o problema contra o documento oficial e classifica a causa:
    ◦ ◇ SE o problema está no documento (erro, desatualização, contradição, gap) → R4-Documento, para a área dona indicada no cabeçalho.
    ◦ ◇ SE o trecho certo existe, mas não veio na busca ou veio junto com a versão errada → R4-Retrieval, para o time técnico.
    ◦ ◇ SE o trecho certo veio, mas a resposta saiu errada (regra invertida, versões misturadas, sem fonte) → R4-Prompt, para o time técnico.
    ◦ ◇ SE não há problema → encerra e explica ao atendente (R7).
• Prioridade [PROPOSTA — validar no discovery]: alta quando envolve carga perigosa, SLA contratual, valor ou prazo, ou quando a resposta já chegou ao cliente.

R3. Correção
• Documento (área dona): corrigir ou publicar o documento e marcar vigência. Exemplos: formalizar se a PROC-042 v1 está obsoleta; decidir se FAQ-32 e FAQ-38 viram procedimento oficial ou saem da base; criar documento para frete abaixo de 500 kg.
• Retrieval (time técnico): ajustar metadados de versão, data de vigência e tipo de fonte, e priorizar documento normativo sobre FAQ. Ajustar o chunking quando uma tabela for cortada.
• Prompt (time técnico): ajustar a instrução que falhou. Exemplo: reforçar que exceção ("NÃO são elegíveis") nunca vira permissão.

R4. Validação
• Documento: a área dona aprova o texto antes da publicação.
• Retrieval e prompt [PROPOSTA — validar no discovery]: rodar de novo a pergunta que gerou o feedback e as perguntas do mapa de cobertura do Anexo B, como teste de regressão.
• ◇ SE o teste falha → volta a R4. SENÃO → R6.

R5. Atualização da base
• Quem age: time técnico.
• O que faz: reindexa o documento corrigido, retira ou marca as versões obsoletas e publica a nova versão de prompt, se for o caso.
• O prazo entre a publicação de um documento e sua disponibilidade no assistente ainda não está definido (pergunta em aberto).

R6. Retorno ao atendente
Gatilho proativo [PROPOSTA — validar no discovery]: a cada publicação mensal de Operações, Compliance ou Comercial, a curadoria verifica se o novo documento substitui ou contradiz outro já indexado, antes que o atendente encontre o problema.

4. Guardrails de comportamento
Os oito guardrails abaixo detalham os quatro já definidos (fonte, não inventar, "não encontrei", português formal e acessível) para os riscos concretos da base da NovaTech. Os cinco primeiros vêm diretamente das armadilhas do Anexo B.

G1. Só existem os tiers Gold, Silver e Standard
• Regra: nunca atribuir SLA a um tier fora desses três. Se a pergunta citar outro, o assistente corrige a premissa, sugere confirmar o contrato do cliente e indica o Comercial para pedidos de SLA diferenciado (SLA-2024, seção 1).
• Risco que evita: inventar um compromisso contratual. A SLA-2024 é "documento contratual" e prevê penalidades por descumprimento (seção 4).
• Exemplo: "Qual o SLA do cliente Platinum?". Resposta errada: "resposta em até 1h e resolução em até 12h". Resposta certa: "A SLA-2024, seção 1, define apenas os tiers Gold, Silver e Standard e diz que não existem outros. Confirme o tier no contrato do cliente."

G2. Exceção nunca vira permissão
• Regra: quando o documento diz que algo "NÃO é elegível", a resposta começa pelo "não" e em seguida dá o destino. O assistente também não promete exceção com base no FAQ.
• Risco que evita: carga perigosa entrar no fluxo padrão de devolução pelo Portal do Cliente.
• Exemplo: "Posso devolver carga perigosa?". Resposta errada: "Sim, em até 7 dias úteis". Resposta certa: "Não pelo processo padrão. Cargas das classes 1 a 6 da ANTT estão fora da devolução padrão; o cliente deve procurar a Gestão de Riscos, ramal 4500, para tratamento individual (POL-001, seção 3.2)." O FAQ-03 diz que "já autorizaram exceção", mas isso não pode ser prometido ao cliente.

G3. Uma versão por cálculo, nunca misturada
• Regra: todos os parâmetros de um cálculo de frete (multiplicador, fator de peso, prazo adicional, desconto) vêm da mesma versão, escolhida pela PROC-042-v2, seção 5, e a versão é citada.
• Risco que evita: um frete que não bate com nenhuma tabela oficial.
• Exemplo: 2.000 kg para o Sudeste. Resposta errada: multiplicador 1.1 (v2) × fator de peso 1.2 (v1). Resposta certa: 1.1 × 1.15, tudo da v2 (seções 2 e 2.1), com aviso de que a v1 segue publicada.

G4. FAQ nunca sustenta informação crítica
• Regra: carga perigosa, avaria ou sinistro, valores, percentuais, prazos e contatos só podem vir de documento normativo ou contratual. O FAQ aparece apenas como "prática relatada, não validada", com aviso.
• Risco que evita: responder com confiança alta a partir de uma fonte que o próprio documento declara não validada.
• Exemplos: FAQ-32 (expresso para carga perigosa, "cerca de 2 dias"), FAQ-38 (48h e sinistros@novatech.com.br) e FAQ-22 (seguro de 0,3% e 0,8%). Nenhum desses temas tem documento formal na base.

G5. Fora do escopo do documento, "não encontrei"
• Regra: não estender uma regra além do escopo que o próprio documento declara.
• Risco que evita: inventar tarifa para o frete padrão.
• Exemplo: "Frete para 300 kg para Salvador?". A PROC-042-v2 vale só acima de 500 kg (seção 1), então a resposta é "não encontrei" e a sugestão é escalar ao supervisor.

G6. Sem dado de entrada documentado, sem número final
• Regra: entregar a fórmula e os parâmetros documentados, mas nunca um valor em R$ ou uma data final que dependa de dado fora da base.
• Risco que evita: um valor ou prazo sem fonte repassado ao cliente.
• Exemplo: "Quanto custa o frete para 600 kg para Manaus?". O valor base está na tabela mensal de fretes e o prazo depende do "prazo padrão da rota" (PROC-042-v2, seções 2 e 3); nenhum dos dois está na base.

G7. Sinalizar incidente crítico e limite de alçada
• Regra: quando o caso bate com um critério de incidente crítico (SLA-2024, seção 3), o assistente avisa e mostra os prazos críticos. Quando a decisão não é do atendente, ele indica quem aprova.
• Risco que evita: perder o SLA crítico e prometer algo além da alçada.
• Exemplos: carga perigosa com documentação irregular é incidente crítico (Gold: resposta em até 30 min, resolução em até 4h). Cargas acima de 5.000 kg exigem aprovação prévia do gerente de operações regional (PROC-042-v2, seção 4). Descontos acima dos previstos exigem a Diretoria Comercial (idem).

G8. Prazo sempre com a unidade do documento
• Regra: repetir a unidade exata: dias úteis ou horas úteis, e o que fica fora da contagem.
• Risco que evita: prometer ao cliente um prazo diferente do contratual.
• Exemplos: "7 dias úteis, excluindo sábados, domingos e feriados nacionais" (POL-001, seção 3.1). O relógio de SLA pausa fora das 08h–18h em dias úteis para chamados gerais, mas não pausa para incidentes críticos de clientes Gold (SLA-2024, seção 5).

```
**Análise crítica**
O assistente não escolhe versão em silêncio. Mostrar as alternativas lado a lado e exigir confirmação humana é o comportamento certo para o conflito do PROC-042.
"Não concordo" não significa "a IA errou". O motivo obrigatório e o atendimento que segue sem ficar bloqueado estão corretos.
Feedback não altera a base automaticamente. O item 45 do FAQ mostra o que acontece quando prática informal entra sem curadoria.
Os três guardrails. São específicos e cada um se apoia numa evidência do 1.1.
Os guardrails identificados são muito importantes para minimizar o risco do assistente assumir algumas inferências como verdade, antes mesmo de validar as informações ja consolidadas e validadas.
É muito importante ter cuidado com as informações do documento FAQ, pois é um documento informal e nao é oficial
```

## Diagrama visual (Claude Design)
2. Usando o **Claude Design**, transforme a jornada textual em um diagrama visual de fluxo que mostre os 3 caminhos (principal, fallback, feedback) de forma clara para apresentar ao time e ao cliente.

[Diagrama de fluxo do atendente](./jornada-atendente-ia-v3.png)
## Entregável

- [x] Jornada textual (fluxo principal, fallback, feedback, guardrails)
- [x] Autocrítica documentada
- [x] Diagrama visual gerado pelo Claude Design
- [x] Evidência do uso das ferramentas (prompt + output nesta página)
