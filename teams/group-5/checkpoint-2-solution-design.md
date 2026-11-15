# Checkpoint 2: desenho da solução

> Grupo 5 · Tratativa com o fornecedor. O desenho segue as issues #155, #156 e #157 da solução e
> o código da branch `grupo-5/tratativa-fornecedor`. Escopo do grupo, depois da mudança de 15/11:
> só "pedir ao fornecedor a nota de devolução", no ramo receber parcial de E1, E2 e E3.

---

## Grupo e ponto de partida

- Grupo: Grupo 5 · Tratativa com o fornecedor
- Perda escolhida no checkpoint 1: Pagar errado (P1)
- Número a mover (baseline → meta): R$ 543 mil por mês (média jan–set, dossiê, Anexo 2) → a confirmar com o grupo
- Frente escolhida: Check antecipado e devolução, na parte da devolução ao fornecedor

## 1. Fluxo atual

Do recebimento com divergência ao pagamento do título.

1. A mercadoria chega com falta, item trocado ou quantidade menor (quem: doca do CD, pelo WMS, ou loja, pelo app)
2. A divergência sobe por relatório diário ou grupo de WhatsApp da loja (quem: CD e lojas)
3. O time cruza ordem, nota e recebido à mão; uma nota de 80 itens leva mais de 1h30 (quem: analistas do contas a pagar)
4. A divergência vira ordem complementar, criada por três áreas, em 2 dias a 3 meses (quem: compras, recebimento e contas a pagar)
5. O título é pago pelo valor da nota e nada é pedido de volta ao fornecedor (quem: contas a pagar e CFO)

- Onde a perda acontece neste fluxo (número do passo): 5, depois de 3 e 4 não fecharem a tempo.
- Por que acontece ali: ninguém transforma a divergência em pedido ao fornecedor. No Anexo 4, a falta física "paga z sem receber" e a quantidade menor "paga integral"; o recuperado é zero (Anexo 3).

## 2. Fluxo proposto

1. **[humano]** A pessoa decide a proposta da doca como receber parcial em E1, E2 ou E3 (quem: doca ou aprovação, em outra parte da solução)
2. **[agente]** Na rodada seguinte do Cron (a cada 15 minutos), a regra monta a nota de devolução só com as linhas devolvidas, e o Claude escreve o e-mail ao fornecedor com cada número só como `{{chave}}` do catálogo de fatos; um verificador descarta o rascunho com número fora do catálogo (quem: Worker da API)
3. **[humano]** O analista revisa o bloco "Pedido ao fornecedor": pode editar o texto do e-mail, mas não os números, os itens nem o que se pede (quem: analista do contas a pagar)
4. **[humano]** O analista clica em "Emitir e enviar": o mock emite a nota (chave e número), a devolução da fila vira `emitida`, e o e-mail sai pela caixa de saída mock com a nota em PDF anexa (quem: analista; emissão e envio pelo Worker)
5. **[agente]** A resposta do fornecedor é lida e ganha um próximo passo; quem passou do prazo (3 dias úteis) ganha o rascunho da cobrança, e uma pessoa confirma ou envia (quem: Worker e analista; issue #157, ainda em construção)

- O que deixa de acontecer: montar de cabeça a nota de devolução e o e-mail; divergência decidida sem pedido ao fornecedor.
- O que passa a acontecer: toda decisão de receber parcial de E1, E2 e E3 vira, em até duas rodadas do Cron (30 minutos), um rascunho pronto para revisar, com os números do banco.

## 3. Componentes

| Componente | O que faz | Entrada | Saída | Sistema ou dado do cliente que usa |
| --- | --- | --- | --- | --- |
| Regra da devolução (`api/src/domain/return-note-plan.ts`, `return-note-emission.ts`) | Diz quais decisões devolvem e monta os itens e o total | Proposta decidida, linhas devolvidas, nota de origem | Rascunho da nota de devolução; o que se pede ao fornecedor por exceção | Decisões da doca (A1), notas (A2) |
| Passo do Cron (`api/src/cron/supplier-requests.ts`) | Abre o pedido das decididas e pede o texto ao Claude; teto de 1 aberto e 1 escrito por rodada | Propostas decididas no Supabase | Pedido em `writing` e depois em `draft` | Supabase (A7) |
| Claude e verificador de números (catálogo de fatos do dossiê) | Escreve o e-mail curto e cordial; descarta o texto com número que não está no catálogo | Fatos da nota, da devolução e do prazo | Texto com `{{chave}}`, ou descarte com o motivo `number_mismatch` | Cadastro de fornecedores (A4) |
| Emissor mock e PDF | Emite a nota no formato do kit, sem duplicar, e gera o PDF anexo | Linha da fila de devolução | Nota com chave de 44 dígitos e número; PDF | Escrita no ERP, simulada (A3) |
| Porta de envio | Envia o e-mail pela caixa de saída mock ou, com a chave, pelo Resend | Pedido aprovado e anexo | E-mail em `outbox_supplier_emails`; status `sent` ou `send_failed` | Envio de e-mail (A5) |
| Rotas e tela (`/api/pedidos-fornecedor`, bloco na gaveta e a página `demo-tratativa/`) | Mostra o pedido e as ações que o estado permite | Chave da nota | Editar, emitir e enviar, reenviar, descartar, refazer | Login pelo Cloudflare Access |
| Leitura da resposta (issue #157, em construção) | Classifica a resposta e prepara a cobrança | Resposta do fornecedor ou prazo vencido | Resultado, resumo, próximo passo, rascunho da cobrança, trecho suspeito | Caixa do contas a pagar (A6) |

Cada sistema ou dado citado aqui deve estar no checklist de acessos.

## 4. Onde entra o agente

- Tarefa do agente, em uma frase: escrever o e-mail ao fornecedor que acompanha a nota de devolução, com os números só como marcadores do catálogo.
- O que o agente recebe: a nota de origem, a classificação (E1, E2 ou E3), as linhas devolvidas, o valor, o fornecedor, o prazo e o que se pede.
- O que o agente devolve: o texto do e-mail em saída estruturada, com `{{chave}}` no lugar de cada número.
- Ferramentas que o agente usa (consultas, leituras, cálculos): o cliente do Claude no Worker (`api/src/claude/client.ts`, issue #171) e o catálogo de fatos do dossiê (`api/src/domain/dossier.ts`), acrescido dos fatos da devolução.
- O que o agente **não** pode fazer: emitir a nota, enviar o e-mail, mudar números, itens ou o que se pede, nem mudar a decisão, o título ou o valor em risco. Texto do fornecedor é dado, nunca ordem (issue #157, AC-4).

## 5. Onde o humano decide

O agente trabalha em **modo proposta**. Nada é emitido nem enviado sem uma pessoa clicar em "Emitir e enviar" (issue #156, AC-3). No código, quem age é o e-mail da identidade do Access; não há faixa de valor no pedido ao fornecedor.

### Alçadas de aprovação

| Alçada | Faixa de valor | Quem decide (papel) | O que a pessoa vê para decidir |
| --- | --- | --- | --- |
| Alçada 1 | até R$ 5 mil | a confirmar com o grupo (pergunta ao cliente) | O bloco "Pedido ao fornecedor": itens e total da nota de devolução, destinatário, prazo, assunto e texto do e-mail |
| Alçada 2 | até R$ 20 mil | a confirmar com o grupo (pergunta ao cliente) | O mesmo bloco |
| Alçada 3 | até R$ 100 mil | a confirmar com o grupo (pergunta ao cliente) | O mesmo bloco |
| Alçada 4 | acima de R$ 100 mil | a confirmar com o grupo; hoje o CFO quer ver cada caso acima de R$ 100 mil (dossiê, "O que cada área disse") | O mesmo bloco |

- Quem decide em cada alçada ainda **não** está definido pelo cliente: o grupo propõe e leva como pergunta.
- O que acontece quando a pessoa discorda da proposta do agente: edita o texto ou descarta o pedido; um descartado pode ser refeito, e o Claude escreve de novo.
- Como a decisão fica registrada: no pedido (`editedBy`, `sentBy`, `discardedBy`, com data e motivo) e no histórico `supplier_request_history`, gravado por gatilho a cada mudança.

## 6. Riscos

| # | Risco | Probabilidade (baixa, média, alta) | Impacto (baixo, médio, alto) | Como o desenho reduz o risco |
| --- | --- | --- | --- | --- |
| R1 | O agente escreve um número errado no e-mail | a confirmar com o grupo | a confirmar com o grupo | Número só como `{{chave}}`; número fora do catálogo descarta o rascunho (AC-6); a pessoa não edita números |
| R2 | Dado de entrada incompleto: fornecedor sem e-mail no cadastro | a confirmar com o grupo | a confirmar com o grupo | O pedido não abre (`no_supplier_email`), mas a devolução entra na fila mesmo assim |
| R3 | A escrita no ERP não é liberada | a confirmar com o grupo | a confirmar com o grupo | Emissor mock no formato do kit; trocar pelo real muda só o conector |
| R4 | Nota emitida duas vezes ou dois pedidos para a mesma nota | a confirmar com o grupo | a confirmar com o grupo | Emitir de novo devolve a mesma nota (AC-4); um pedido aberto por nota e tipo (AC-8); reenviar não reemite |
| R5 | A resposta do fornecedor tenta dar ordem ao agente | a confirmar com o grupo | a confirmar com o grupo | Texto do fornecedor é dado; o trecho vai para `injection_flag` e nada no caso muda (issue #157) |
| R6 | A equipe do cliente não usa a solução | a confirmar com o grupo | a confirmar com o grupo | O bloco fica na gaveta da proposta, onde o analista já decide |

## 7. O que se repete na Terminal Sul

A **Terminal Sul** é uma concessionária de aeroporto. O case não traz mais dados sobre ela; o que segue é hipótese do grupo.

| Parte da solução | Serve igual | Serve com ajuste (qual) | Não serve (por quê) |
| --- | --- | --- | --- |
| Fluxo proposto | Decisão humana → rascunho → revisão → envio → leitura da resposta | a confirmar com o grupo | a confirmar com o grupo |
| Componentes | Porta de envio, verificador de números, emissor atrás de um conector | Regra de devolução: as exceções e as decisões que devolvem | a confirmar com o grupo |
| Agente | Escrever o pedido com números só do catálogo | O texto e o que se pede, por tipo de divergência | a confirmar com o grupo |
| Alçadas e decisão humana | Modo proposta, nada sai sem uma pessoa | Quem decide em cada faixa | a confirmar com o grupo |
| Acessos e dados | Mesmo tipo de pedido: decisões, notas, cadastro, e-mail | Sistemas e donos da Terminal Sul | a confirmar com o grupo |

- O que é o **núcleo reaproveitável** da solução, em uma frase: transformar uma decisão humana sobre uma divergência em um pedido ao fornecedor com números verificados, que só sai com uma pessoa.
- O que é específico da Já Chegou: as exceções E1 a E8, o formato das notas do kit, a doca do CD e as lojas escuras.

## Conferência antes de entregar

- [ ] Fluxo atual e proposto têm o mesmo começo e o mesmo fim.
- [ ] Todo passo marcado [agente] tem um passo [humano] de decisão depois dele.
- [ ] As quatro alçadas estão preenchidas ou marcadas como pergunta ao cliente.
- [ ] Pelo menos três riscos com mitigação.
- [ ] A tabela da Terminal Sul está completa.
