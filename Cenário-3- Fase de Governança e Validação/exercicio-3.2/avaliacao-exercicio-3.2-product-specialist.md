# Avaliação do Exercício 3.2 — Harness de produto para melhoria contínua

| Campo | Valor |
|---|---|
| **Papel** | Product Specialist |
| **Cenário** | 3 — Governança e Validação |
| **Participante** | Jaqueline Santos |
| **Referências usadas** | `avaliacao-product-specialist.md` (cenário 3) e `prompt-avaliacao.md` (cenário 3) |
| **Não disponíveis nesta avaliação** | `avaliacao-foundation.md` (cenário 3) e os Anexos A, B e C |
| **Versão avaliada** | `docs/harness-de-produto.md` v1 |
| **Data** | 30/09/2026 |

> [!WARNING]
> **Conflito de interesse do avaliador.** O documento entregue é **idêntico** ao rascunho que eu (Claude) redigi nesta mesma conversa, a pedido da participante. Não há nenhuma alteração: os valores marcados com [PROPOSTA] continuam como propus e não há registro das decisões da autora. Por isso:
> - separei a **qualidade do conteúdo** (D1, D3 e D5, que avaliam o artefato) do **mérito da participante** (D2 e D4, que avaliam uso da ferramenta e julgamento próprio);
> - revisei o meu próprio texto com o mesmo rigor aplicado nos exercícios anteriores, e os problemas estão listados abaixo;
> - o avaliador humano deve levar isso em conta.

---

## Resumo

Como artefato, o harness atende aos três critérios do exercício:

- **Feedback:** o processo vai do atendente até a melhoria em produção, com triagem por causa.
- **Regressão:** a suíte tem quatro camadas, e os guardrails do cenário 2 funcionam como invariantes que bloqueiam a mudança.
- **HITL:** o controle de mudanças tem dez tipos, cada um com quem aprova e a evidência exigida.

O conteúdo tem, porém, duas falhas que comprometem os próprios objetivos (dados pessoais e uma checagem da camada C1). Além disso, a entrega não mostra julgamento próprio nem registra como o Claude foi usado.

---

## Scores por dimensão

| Dimensão | Score | Justificativa |
|---|---|---|
| D1 — Domínio Conceitual | 3 | O conteúdo trata os guardrails como invariantes, reconhece efeitos colaterais de cada tipo de mudança, trata o não determinismo (3 execuções), separa o HITL de mudança do HITL de resposta (3.1), prevê canário e rollback e calcula a confiança por código. |
| D2 — Uso de Ferramentas | 2 | O Claude foi usado, e isso aparece nesta conversa. Mas o entregável não traz o prompt, o contexto dado nem o que foi aceito ou mudado no rascunho. |
| D3 — Qualidade do Entregável | 2 | O documento está completo e bem estruturado. Mas tem duas contradições que afetam o que ele se propõe a garantir: o registro de feedback guarda dados do cliente, e uma checagem da C1 reprova respostas corretas (problemas 1 e 2). É o mesmo tipo de falha que rebaixou D3 no exercício 2.2. |
| D4 — Pensamento Crítico | 1 | O rascunho foi entregue sem nenhuma alteração. Nenhum valor [PROPOSTA] foi validado ou justificado, e não há análise do que o Claude propôs. Não há como atribuir à participante o julgamento que aparece no texto. |
| D5 — Aplicabilidade ao Projeto | 3 | O conteúdo se conecta aos artefatos anteriores: guardrails do 2.2, `AG-*` do 2.3, VCs do 2.1, revisão do 3.1, ADR-0001, 0002 e 0004, Anexo B, os 5 atendentes-piloto, a meta de 30 s e a demonstração em 2 semanas. |

**Score do exercício: 2,2**

---

## Checklist da skill (Exercício 3.2), avaliando o conteúdo

| Critério | Nota | Evidência |
|---|---|---|
| Processo de feedback completo | 3 | Sinais manuais e automáticos, triagem em 7 causas com dono, golden query antes da correção, regressão, aprovação, canário, rollback e retorno a quem reportou. |
| Regression testing de produto | 3 | Tabela de efeitos colaterais por tipo de mudança; camada C1 com 9 golden queries ligadas aos IDs dos guardrails e bloqueio a qualquer falha; baseline × candidata; matriz de camadas por tipo de mudança. Ressalva: problema 2. |
| Ponto de HITL | 3 | Dez tipos de mudança, com risco, aprovadores e evidência exigida em cada um; regra de que aprovação não substitui a suíte; modelo de PR. Os nomes dos aprovadores estão corretamente como pendência da NovaTech. |
| Preserva os guardrails do cenário 2 | 3 | O princípio 1 declara os guardrails como invariantes. A C1 exige 100% nas 3 execuções, e remover uma golden query da C1 exige aprovação (H2). |

**Armadilhas:** a skill não lista armadilhas obrigatórias para o 3.2.

---

## Problemas encontrados no conteúdo

1. **O registro de feedback repete o incidente que devia evitar.** A seção 2.1 diz que o registro "não guarda nenhum dado do cliente final (nome, CT-e, contrato)". Mas o mesmo registro guarda **a pergunta e a resposta** na íntegra, e são justamente elas que trazem CT-e, nome do cliente e número de contrato ("Cliente X, CT-e 123, quer devolver…"). **Correção:** mascarar a pergunta e a resposta antes de gravar (CT-e, CPF/CNPJ, e-mail, nomes) e acrescentar à C4 uma golden query que verifica esse mascaramento.

2. **O assert da golden query GQ-C1-001 reprova a resposta correta.** O `must_not_contain` é "pode ser devolvida pelo processo padrão". A resposta correta, "Carga perigosa **não** pode ser devolvida pelo processo padrão", contém essa frase e seria reprovada. Além disso, "contém 'processo padrão' e '4500'" só verifica se os termos aparecem, e não o sentido. Foi o limite apontado nas avaliações do 2.2 e do 2.3. **Correção:** trocar por um padrão que exclua a negação, ou deixar essa verificação de sentido para a C3 (avaliação humana ou por LLM), mantendo na C1 só a parte determinística.

3. **O LLM avaliador da C3 não tem calibração.** No 3.1, o próprio Claude errou o peso da resposta 2. Se o LLM avaliador faz a primeira passada, é preciso medir periodicamente a concordância dele com as notas humanas numa amostra, e revisar o avaliador quando essa concordância cair.

4. **O canário tem amostra pequena.** Com 5 atendentes durante 3 dias, a métrica M7 (feedback "Não útil") não tem volume para detectar piora. No canário, só M2 e M3 (determinísticas) são sinais confiáveis. O documento deveria dizer isso, ou ampliar o grupo e o período para mudanças de risco médio.

5. **O H10 subestima o impacto de validação mais rígida.** Um validador mais restritivo **muda** o comportamento, porque bloqueia mais respostas e aumenta o `not_found` e o HITL-01. O H10 deveria exigir também a C3 e acompanhar M5 e M9.

6. **Detalhes:**
   - "A curadoria triagem cada item" deveria ser "A curadoria faz a triagem de cada item".
   - O "plantão" que pode fazer rollback não tem responsável definido.
   - Para a causa D1 (documento ausente), o comportamento correto (lacuna ou rótulo de informal) talvez já passe no teste, e aí a regra "a primeira execução deve falhar" não se aplica.

---

## Pontos fortes (do conteúdo)

1. **Os guardrails funcionam como trava real.** A C1 bloqueia a mudança, a aprovação não passa por cima da suíte e remover um teste também exige aprovação. Isso fecha o caminho de passar no teste apagando o teste.
2. **A triagem por causa leva à melhoria certa.** Documento, recuperação, prompt e regra de produto chegam a donos diferentes, e há exemplos reais do 3.1.
3. **Existe um mínimo viável para a demonstração em 2 semanas.** O documento prioriza schema, C1, HITL-01 e botão de feedback, que é o que impede os casos #4 e #6 na frente da diretoria.

---

## O que fazer antes de entregar

1. **Mostrar o seu julgamento sobre o rascunho.** É o que mais pesa (D4 de 1 para 2 ou 3). Acrescente uma seção curta, "Decisões da autora sobre o rascunho do Claude", com:
   - cada valor [PROPOSTA] que você manteve, mudou ou rejeitou, e por quê (por exemplo: meta de M1 < 5%, tolerância de 2 p.p. na C3, 3 execuções, 3 dias de canário);
   - pelo menos um ponto do rascunho de que você discorda ou que priorizou de outro jeito.
2. **Corrigir os problemas 1 e 2** (dados pessoais na pergunta e na resposta; assert que reprova a resposta correta). É rápido e leva D3 a 3.
3. **Registrar o uso do Claude:** o prompt enviado (inclusive a sua resposta "projete um harness…"), o contexto dado (guardrails, AGENTS.md, revisão do 3.1) e esta avaliação como revisão. Isso leva D2 a 3.
4. **Opcional:** resolver os problemas 3 a 6.

Com os itens 1 a 3 feitos, a nota provavelmente fica entre 2,8 e 3,0.

---

## Classificação

**Aprovado (2,2)**

## Tópicos da trilha para reforço

- **Revisão Crítica de Outputs de IA** aplicada ao próprio artefato: revisar e adaptar o que o Claude produziu antes de entregar, registrando o que foi aceito e o que foi mudado.
- **Harness Engineering:** proteção de dados no registro de feedback e calibração de LLM usado como avaliador.

---

> **Observação:** esta avaliação foi feita por IA e não substitui a validação do avaliador humano. Isso vale ainda mais aqui, porque o conteúdo avaliado foi redigido pelo próprio avaliador.
