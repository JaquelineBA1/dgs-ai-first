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
Sou o Product Specialist do projeto de assistente de IA da NovaTech (empresa de
logística). Já concluímos a fase de discovery e agora preciso mapear a jornada do
atendente usando esse assistente.

Dados do discovery:
- Os atendentes hoje abrem em média 4 fontes diferentes por chamado.
- As dúvidas mais comuns são: prazos de entrega (35%), regras de frete (25%),
  política de devolução (20%), outros (20%).
- Em 15% dos casos, o atendente não encontra resposta e escala para o supervisor.

Contexto adicional do projeto: já identificamos que a documentação da NovaTech tem
problemas reais — duas versões contraditórias do procedimento de frete especial
(PROC-042 v1 e v2, sem indicação de qual está vigente), e um FAQ informal do time
de atendimento que contém práticas sem respaldo em nenhum documento oficial.

Elabore a jornada do atendente usando o assistente, em formato de texto
estruturado, cobrindo:

Fluxo principal (caminho feliz): do momento em que o atendente recebe a dúvida
do cliente até usar a resposta do assistente no atendimento.

Fluxo de fallback: o que acontece quando o assistente não tem confiança
suficiente na resposta, ou quando o atendente discorda do que foi retornado.

Fluxo de feedback: como o atendente sinaliza que uma resposta estava errada,
desatualizada ou incompleta, e o que acontece com esse sinal depois.

Pelo menos 2 guardrails de comportamento do assistente, específicos ao domínio
da NovaTech (não genéricos) — por exemplo, ligados diretamente aos problemas
reais de documentação que já identificamos (as duas versões do PROC-042, ou o
FAQ sem validação formal).

Depois de gerar a primeira versão, quero que você mesmo aponte se algum dos fluxos
ficou incompleto ou genérico demais, para eu poder refinar antes de passar isso
para o Claude Design.

```
### Output obtido (V1):

```
**Jornada do Atendente com o Assistente de IA — NovaTech**
1. Fluxo principal (caminho feliz)

O atendente recebe a dúvida do cliente (chat, telefone ou e-mail) e já a reconhece dentro de uma das categorias mapeadas no discovery — prazo de entrega, regra de frete, devolução ou outros. Em vez de decidir sozinho qual das 4 fontes hoje consultadas é a certa, ele abre o assistente (idealmente embutido na mesma tela de atendimento, não uma ferramenta à parte) e digita a pergunta em linguagem natural, como faria com um colega mais experiente.

O assistente identifica a intenção da pergunta e busca apenas nas fontes normativas vigentes para aquele tema — não em tudo que existir sobre o assunto. Ele devolve uma resposta única, com três elementos sempre visíveis: a resposta objetiva, a fonte exata citada (documento, versão e seção — por exemplo "PROC-042 v2, seção 2.1"), e um indicador de status da fonte (normativa validada vs. prática informal não validada). O atendente lê, confirma que a resposta se aplica ao caso concreto do cliente, e a usa diretamente na resposta — copiando, adaptando ou parafraseando. O chamado é registrado como resolvido, e o sistema guarda qual fonte foi usada, o que permite depois medir quais documentos são mais citados e onde a confusão é maior.

Esse fluxo, por si, já ataca dois números do discovery: reduz as 4 consultas manuais a uma única interação, e deveria reduzir a fatia dos 15% de escalonamentos que hoje acontecem simplesmente porque o atendente não encontrou a resposta em nenhuma das fontes espalhadas.

2. Fluxo de fallback

Existem dois gatilhos distintos, e eles merecem tratamento diferente.

Quando o assistente tem baixa confiança — por exemplo, porque a pergunta cai exatamente no caso que já identificamos como sem vigência clara (cálculo de frete especial, com PROC-042 v1 e v2 coexistindo) — ele não deve escolher uma versão silenciosamente. Em vez disso, apresenta as duas alternativas lado a lado, destaca a diferença entre elas e sinaliza explicitamente que precisa de confirmação humana antes de qualquer resposta ser dada ao cliente. O atendente então decide: escalar para o supervisor (já com o contexto resumido pelo assistente, para que o escalonamento não comece do zero) ou buscar confirmação em outro canal antes de responder.

Quando o atendente discorda da resposta — por achar que ela não bate com a prática real, ou porque o cliente contesta — ele marca "não concordo" com um motivo curto obrigatório. Esse sinal não deve ser tratado automaticamente como "o assistente errou": ele abre um caso de revisão humana, porque o atendente tanto pode estar certo (documentação desatualizada) quanto errado (prática informal desalinhada da política oficial). Enquanto o caso de revisão não é resolvido, o atendimento ao cliente segue pelo escalonamento humano padrão, sem ficar bloqueado esperando uma correção da base.

3. Fluxo de feedback

O atendente sinaliza o problema com uma ação simples — algo como marcar a resposta como "correta", "desatualizada", "incompleta" ou "não encontrada" — sem precisar preencher um formulário longo no meio do atendimento. Um campo curto de "o que deveria ser a resposta" fica disponível, mas opcional, para não gerar fricção.

Esse sinal não altera a base de conhecimento automaticamente — vai para uma fila de curadoria humana, o que é especialmente importante no caso da NovaTech, já que vimos no FAQ um exemplo real de prática informal entrando "por baixo" sem validação (o item que já mistura os critérios de desconto da v1 e da v2). A fila é revisada periodicamente por quem tiver a propriedade de conteúdo definida (Operações e/ou Comercial, dado que já identificamos que o dono de vários desses temas é compartilhado entre as duas diretorias), e cada item é resolvido de um de três jeitos: atualizar a fonte oficial, confirmar que a resposta do assistente estava certa e descartar o feedback, ou abrir uma ação de formalização quando o problema é a ausência total de documento (como o processo de sinistro por carga danificada). O atendente que reportou recebe uma notificação de volta quando o item for tratado — mesmo que a decisão seja "revisado, sem mudança" — porque sem esse fechamento de loop as pessoas param de reportar.

4. Guardrails específicos ao domínio NovaTech

O primeiro guardrail: o assistente nunca responde uma pergunta de cálculo de frete especial citando a PROC-042 com um único número, a menos que exista uma indicação explícita e confiável de qual versão está vigente para aquele caso. Na ausência dessa indicação — que é o estado real hoje, confirmado no discovery — ele deve mostrar as duas versões com a diferença destacada e pedir confirmação humana. Ele nunca deve aplicar a heurística "usar sempre a versão mais recente" por padrão, porque já vimos, no próprio FAQ, que essa heurística já produziu uma regra híbrida inválida na prática humana (misturando o limiar da v1 com o mecanismo da v2).

O segundo guardrail: qualquer informação vinda exclusivamente do FAQ-Atendimento, sem respaldo em nenhum documento normativo, precisa ser sinalizada explicitamente como prática informal não validada — nunca apresentada com o mesmo peso e o mesmo tom de uma política oficial. Isso vale com força redobrada para temas sensíveis como carga perigosa e sinistro, onde o próprio FAQ já mostra que a prática usada diverge da regra formal.

O terceiro guardrail (bônus, ligado a um risco que já identificamos no discovery): em qualquer caso que envolva carga perigosa ou valor de indenização/reembolso, o assistente nunca fecha o atendimento sozinho, mesmo que "encontre" uma resposta — ele sempre força o encaminhamento a um humano (Gestão de Riscos ou Jurídico), porque já identificamos que a autoridade para conceder exceções nesses casos não está documentada em lugar nenhum do nosso lote.

Autocrítica — o que ficou incompleto ou genérico demais

Alguns pontos merecem seu refinamento antes de seguir para o Claude Design.

O fluxo principal não define o canal/interface real (assistente embutido no sistema de atendimento? painel lateral? comando por voz no telefone?) — escrevi "idealmente embutido na mesma tela", mas isso é uma suposição minha, não um dado do discovery, e muda bastante a experiência, principalmente para atendimento por telefone, onde ler uma resposta longa em tempo real é diferente de copiar um texto num chat.

O gatilho de "baixa confiança" no fluxo de fallback ficou qualitativo — não defini um limiar (score de confiança, ausência de fonte, contradição detectada) porque o discovery não trouxe esse dado. Isso precisa ser decidido com quem for construir o motor de busca/recuperação, não é algo que dá para inventar aqui.

A cadência de revisão da fila de feedback ("periodicamente") e o dono dessa fila ficaram propositalmente vagos, porque essa é uma decisão de governança que a NovaTech ainda não validou com a gente — vale tratar isso como uma pergunta aberta de discovery, não como algo já resolvido na jornada.

O bucket "outros" (20% das dúvidas, quase tão grande quanto devolução) não tem nenhum fluxo dedicado — a jornada trata só de prazo, frete e devolução porque foram os únicos temas em que já investigamos problemas de documentação. Isso é uma lacuna real: um quinto dos chamados está sem cobertura nenhuma na jornada.

Por fim, os guardrails cobrem muito bem frete especial e FAQ/carga perigosa, porque são os dois pontos que já investigamos a fundo — mas "prazos de entrega" é a maior categoria de dúvida (35%) e não tem nenhum guardrail específico, simplesmente porque ainda não analisamos se a documentação de prazos tem os mesmos problemas de versionamento que achamos no frete. Vale considerar isso prioridade para a próxima rodada de análise documental antes de fechar a jornada. 
```
**Análise crítica da V1**
O assistente não escolhe versão em silêncio. Mostrar as alternativas lado a lado e exigir confirmação humana é o comportamento certo para o conflito do PROC-042.
"Não concordo" não significa "a IA errou". O motivo obrigatório e o atendimento que segue sem ficar bloqueado estão corretos.
Feedback não altera a base automaticamente. O item 45 do FAQ mostra o que acontece quando prática informal entra sem curadoria.
Os três guardrails. São específicos e cada um se apoia numa evidência do 1.1.
```

## Diagrama visual (Claude Design)
2. Usando o **Claude Design**, transforme a jornada textual em um diagrama visual de fluxo que mostre os 3 caminhos (principal, fallback, feedback) de forma clara para apresentar ao time e ao cliente.

[Diagrama de fluxo do atendente](./jornada-atendente-ia-v3.png)
## Entregável

- [x] Jornada textual (fluxo principal, fallback, feedback, guardrails)
- [x] Autocrítica documentada
- [x] Diagrama visual gerado pelo Claude Design
- [x] Evidência do uso das ferramentas (prompt + output nesta página)
