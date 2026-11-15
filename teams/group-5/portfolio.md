# A divergência decidida na doca vira nota de devolução e pedido ao fornecedor, revisados por uma pessoa

Grupo: Grupo 5 · Tratativa com o fornecedor · Integrantes: Djegao (líder), pedroduff, augustotechrecruiter; demais a confirmar com o grupo · Formação FDE, 13 a 15/11/2026

## 1. Contexto

- Cliente: Já Chegou, varejista de entrega rápida (caso fictício).
- O processo: contas a pagar de ponta a ponta.
- O pedido do CFO: automatizar as cerca de 400 propostas de pagamento do dia e parar de pagar cerca de R$ 120 mil por mês de juros e multa ([briefing](../../case/briefing.md)).
- Quem participou do lado do cliente (por papel): CFO, controller e gerente de compras e comercial.

## 2. Diagnóstico

- O problema, como o grupo entendeu: a Já Chegou paga mercadoria que não recebeu, e nada volta. A doca vê a falta, a troca ou a quantidade menor, mas ninguém pede a devolução ao fornecedor.
- A perda escolhida e o número a mover (baseline → meta): pagar errado, R$ 543 mil por mês (média jan–set) → meta a confirmar com o grupo. O recuperado de fornecedores é zero em todos os meses ([dossiê](../../case/dossier/README.md), Anexos 2 e 3).
- A reformulação do pedido, e por quê: de "automatizar boleto" para "toda divergência decidida vira nota de devolução e pedido ao fornecedor". P1 é cerca de 4,5 vezes os juros e multas (R$ 122 mil).
- A priorização das frentes (retorno, tempo até o primeiro valor, esforço): notas a confirmar com o grupo. A frente escolhida foi "Check antecipado e devolução", na parte da devolução ([checkpoint 1](checkpoint-1-diagnosis.md)).

## 3. Desenho

- Fluxo atual → fluxo proposto (três a cinco passos cada):
  - Hoje: a doca recebe com divergência → a divergência sobe por relatório ou WhatsApp → o time cruza à mão → ordem complementar em 2 dias a 3 meses → o título é pago e nada é pedido de volta.
  - Proposto: a pessoa decide receber parcial (E1, E2, E3) → a regra monta a nota de devolução e o Claude escreve o e-mail → o analista revisa → "Emitir e enviar" → a resposta do fornecedor é lida e quem atrasa é cobrado.
- Onde entra o agente: só no texto do e-mail, com cada número como `{{chave}}` do catálogo de fatos ([desenho](checkpoint-2-solution-design.md)).
- Onde o humano decide (modo proposta e alçadas R$ 5 mil / R$ 20 mil / R$ 100 mil): nada é emitido nem enviado sem um clique (issue #156, AC-3). Quem decide em cada alçada ficou como pergunta ao cliente; hoje o CFO quer ver cada caso acima de R$ 100 mil.
- Acessos necessários e principais riscos: decisões da doca, leitura do ERP, e-mail dos fornecedores, emissão da nota e caixa de e-mail. Riscos: número errado no e-mail, fornecedor sem e-mail, escrita no ERP não liberada, emissão duplicada e ordem escondida na resposta do fornecedor ([checklist](checkpoint-2-access-checklist.md)).

## 4. Construção

- O que foi construído (issue #156 da solução):
  - a regra da devolução e a nota de devolução emulada, no formato das notas do kit;
  - o passo do Cron que abre o pedido e pede o texto ao Claude;
  - as rotas `/api/pedidos-fornecedor` (editar, emitir e enviar, reenviar, descartar, refazer);
  - a persistência no Supabase (`supplier_contacts`, `supplier_requests`, `return_note_emissions`, `outbox_supplier_emails`);
  - o PDF da nota anexo ao e-mail;
  - o bloco "Pedido ao fornecedor" no painel e uma página de demonstração à parte (`demo-tratativa/`).
- Como funciona, em poucos passos:
  1. A cada 15 minutos o Cron acha as propostas decididas como receber parcial de E1, E2 ou E3.
  2. A devolução entra na fila de notas de devolução; o pedido abre em `writing`.
  3. O Claude escreve o texto; um número fora do catálogo descarta o rascunho.
  4. O analista revisa e clica em "Emitir e enviar": o mock emite a nota (chave de 44 dígitos e número) e o e-mail sai pela caixa de saída mock.
- Decisões técnicas e por quê:
  - Números só como `{{chave}}`: o modelo não inventa valor; o texto é checado contra o banco.
  - Porta de envio com mock como padrão e Resend opcional: funciona sem chave nenhuma (AC-5).
  - Teto de 1 pedido aberto e 1 escrito por rodada: o Worker tem 50 subrequests por invocação no plano gratuito.
  - Página de demo à parte do painel: os pacotes privados do painel dão 403 fora da organização.
- Links para os pull requests principais (fork líder [`Djegao/grupo-5-slucao`](https://github.com/Djegao/grupo-5-slucao)):
  - Mesclados: #22, #23, #24, #26, #27, #28, #29, #30, #32, #37.
  - Abertos: #31, #38, #39 (leitura da resposta, regra do domínio), #41.
  - #40: versão alternativa da #156, da branch `grupo-5/tratativa-fornecedor`, em rascunho para comparar.

## 5. Eval

- Contra o que o agente foi avaliado: a confirmar com o grupo. O grupo não tem um eval do texto do agente com casos rotulados. Há testes automáticos dos critérios de aceite da #156.
- Resultado: na branch `grupo-5/tratativa-fornecedor`, em 15/11, 133 testes passaram em 8 arquivos de `api/test/supplier-requests/`, e 7 testes da API falsa da demo também passaram. Resultado de um eval do agente: a confirmar com o grupo.
- Onde o agente erra e o que o grupo fez a respeito: a confirmar com o grupo, com os números de um eval. O desenho já trata o erro mais caro: o rascunho com número fora do catálogo é descartado pelo sistema (`number_mismatch`) e não chega a ser enviado.

O que deu errado e o que o grupo mudou:

- A recusa com mercadoria já entrada estava no mínimo da issue, mas pelo BPMN quem recusa não recebe. O grupo tirou a recusa e deixou só receber parcial de E1, E2 e E3.
- O dono do case mudou o escopo em 15/11: o cancelamento da nota foi para o backlog.
- Um fornecedor sem e-mail deixava a devolução fora da fila. A correção põe a devolução na fila antes de checar o e-mail.
- A parte web dependia de pacotes privados. O grupo tirou a web do `make check` (PR #28) e fez a página de demo à parte.

## 6. Resultado

- O que muda para o cliente, ligado ao número a mover: hoje o recuperado é zero e o P1 é de R$ 543 mil por mês; com a solução, toda decisão de receber parcial de E1, E2 e E3 vira um pedido de devolução pronto para enviar. Quanto do P1 isso recupera: a confirmar com o grupo.
- O primeiro valor e quando ele chegaria: a primeira nota de devolução enviada sem ninguém montar o texto, na Fase 2 do [plano](checkpoint-2-delivery-plan.md); prazo a confirmar com o grupo.
- O que se repete na Terminal Sul: a decisão humana virando pedido ao fornecedor com números verificados; muda a regra de quais decisões devolvem e os conectores.
- Próximos passos: terminar a leitura da resposta e a cobrança (issue #157; PR #39 e issues #33 a #36 do fork líder); pedir ao cliente os e-mails dos fornecedores e a decisão sobre a emissão da nota.

## 7. Aprendizados

- O que o grupo faria igual: a confirmar com o grupo
- O que o grupo faria diferente: a confirmar com o grupo
- O que cada integrante aprendeu, em uma frase: a confirmar com o grupo
