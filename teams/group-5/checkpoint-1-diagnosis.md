# Checkpoint 1: diagnóstico e prioridade

> Grupo 5 · Tratativa com o fornecedor. Fontes: `case/briefing.md`, `case/dossier/README.md` e as
> issues #155, #156 e #157 da solução. Onde o valor não tem fonte, está escrito "a confirmar com o grupo".

---

## Grupo

- Grupo: Grupo 5 · Tratativa com o fornecedor
- Integrantes: Djegao (líder), pedroduff, augustotechrecruiter; demais a confirmar com o grupo

## 1. O problema

- O que o cliente disse que é o problema: o CFO quer automatizar as cerca de 400 propostas de pagamento do dia (lançar, pagar e baixar sem digitar) e parar de pagar juros e multa, cerca de R$ 120 mil por mês (briefing, "O pedido do CFO").
- O que o grupo entende que é o problema: a Já Chegou paga mercadoria que não recebeu e nada volta. A doca vê a divergência (falta, item trocado, quantidade menor), mas ninguém pede ao fornecedor a nota de devolução ou o abatimento.
- Onde, no caminho do pedido de compra até o pagamento, ele aparece: entre o recebimento com divergência e o pagamento do título (dossiê, passos 4 a 6 e 10; Anexo 4, exceções 1 a 3).
- Quem sente a dor (por papel: CFO, controller, compras, outro): controller (paga sem mercadoria e provisiona no fechamento), analistas do contas a pagar (montam a tratativa à mão), CFO (caixa).
- Evidência (dossiê ou reunião): P1 de R$ 543 mil por mês e valor recuperado de fornecedores igual a zero em todos os meses (dossiê, Anexos 2 e 3); fala do controller: "Paga-se em média R$ 543 mil por mês sem mercadoria correspondente, e nada volta".

## 2. O número a mover

### Qual perda

- [x] Pagar errado
- [ ] Pagar atrasado
- [ ] Ruptura de estoque
- [ ] Custo de operar

### Baseline e meta

| Campo | Valor | Fonte |
| --- | --- | --- |
| Indicador (como a perda é medida) | Pago sem mercadoria correspondente, menos o recuperado | Dossiê, Anexo 2 (P1) |
| Baseline (valor hoje) | R$ 543 mil por mês (média); faixa de R$ 136 mil a R$ 1,8 milhão | Dossiê, Anexo 2 |
| Período do baseline | Janeiro a setembro | Dossiê, Anexo 2 |
| Meta | a confirmar com o grupo | — |
| Prazo da meta | a confirmar com o grupo (o projeto tem oito semanas) | Briefing, "O que a Barte foi contratada para fazer" |

- Por que esta perda, e não as outras três: P1 (R$ 543 mil) é cerca de 4,5 vezes P2 (R$ 122 mil) e 12 vezes P4 (R$ 45 mil) (dossiê, Anexo 2). E o recuperado é zero: qualquer devolução cobrada já move o número.
- Como medir com tanta variação mensal: a confirmar com o grupo (o dossiê avisa que abril concentrou quase 40% do P1).

## 3. A prioridade

As quatro frentes são as do dossiê ("Frentes que o time já discutiu"). As notas não foram registradas pelo grupo.

| Frente | Retorno | Tempo até o primeiro valor (5 = mais rápido) | Esforço (5 = menor esforço) | Total |
| --- | --- | --- | --- | --- |
| Check antecipado e devolução | a confirmar com o grupo | a confirmar com o grupo | a confirmar com o grupo | a confirmar com o grupo |
| Conciliação ordem × nota × recebido | a confirmar com o grupo | a confirmar com o grupo | a confirmar com o grupo | a confirmar com o grupo |
| Priorização de caixa e aprovação | a confirmar com o grupo | a confirmar com o grupo | a confirmar com o grupo | a confirmar com o grupo |
| Cadastro e ordem complementar | a confirmar com o grupo | a confirmar com o grupo | a confirmar com o grupo | a confirmar com o grupo |

- Frente escolhida para começar: Check antecipado e devolução, na parte final dela: a divergência decidida na doca vira nota de devolução e pedido ao fornecedor.
- Por quê (em uma ou duas frases, ligando ao número a mover): o P1 é dinheiro pago sem mercadoria; a nota de devolução é o que faz esse dinheiro voltar, e hoje o recuperado é zero.
- O que fica para depois, e por quê: no escopo do grupo, o pedido de cancelamento (E6 duplicidade e recusa antes da entrada) foi para o backlog na mudança de escopo de 15/11 (issues #155 e #156). As outras frentes ficam com outros grupos ou depois.

## 4. A reformulação do pedido do CFO

| | Texto |
| --- | --- |
| O pedido, como o CFO trouxe | Automatizar boletos (lançar, pagar e baixar sem digitar) e parar de pagar cerca de R$ 120 mil por mês de juros e multa. |
| O pedido, como o grupo propõe | Parar de pagar errado: toda divergência que a doca decide receber parcial vira, no mesmo dia, nota de devolução e e-mail ao fornecedor, revisados por uma pessoa. |
| Por que a mudança move mais o número | P1 vale R$ 543 mil por mês contra R$ 122 mil de P2, e nada do P1 volta hoje (dossiê, Anexos 2 e 3). |
| Como o grupo vai apresentar isso ao CFO | a confirmar com o grupo |

Se o grupo decidir **manter** o pedido como veio, explique por quê: não se aplica; o grupo reformulou.

## 5. Hipóteses e o que falta saber

### Hipóteses

| # | Hipótese | Como confirmar | Se estiver errada, o que muda |
| --- | --- | --- | --- |
| H1 | Parte do P1 vem de falta, item trocado e quantidade menor (exceções 1 a 3) que a doca já vê, mas que não viram pedido ao fornecedor. | Cruzar as divergências registradas no recebimento com os títulos pagos integralmente. | O ganho da tratativa é menor; o peso vai para duplicidade e nota sem ordem. |
| H2 | O fornecedor atende quando recebe a nota de devolução com os números e um prazo. | Medir as respostas aos primeiros pedidos enviados (issue #157). | É preciso cobrança e escalada, não só o pedido. |
| H3 | O jurídico aceita a nota de devolução depois da entrada da mercadoria. O controller citou: "aceitar a nota é aceitar o pedido". | Perguntar ao jurídico, pelo controller. | A devolução precisa acontecer antes da entrada (check antecipado). |

### O que falta saber

| # | Pergunta | Para quem (por papel) | Por que importa |
| --- | --- | --- | --- |
| Q1 | Quem emite nota de devolução hoje, e em qual sistema? | Controller | A escrita no ERP não foi pedida e a homologação é uma cópia de março (dossiê, Anexo 6). |
| Q2 | Onde está o e-mail de contato fiscal de cada fornecedor? | Gerente de compras e comercial (cadastro de fornecedores) | Sem e-mail, o pedido não sai. |
| Q3 | Quanto do P1 é falta, troca e quantidade menor, e quanto é duplicidade ou nota sem ordem? | Controller | Diz quanto do número a tratativa pode mover. |

## Conferência antes de entregar

- [ ] Uma perda marcada, com baseline, meta e fonte.
- [ ] Quatro frentes pontuadas e uma escolhida, com o porquê.
- [ ] Pedido do CFO reformulado (ou mantido, com justificativa).
- [ ] Pelo menos três hipóteses e três perguntas abertas.
- [ ] Nenhuma pessoa citada pelo nome: só pelo papel.
