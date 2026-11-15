# Checkpoint 2: plano de entrega

> Grupo 5 · Tratativa com o fornecedor. As fases seguem o "Plano (grupo 5)" da issue #156 e as
> issues do fork líder (`Djegao/grupo-5-slucao`). As durações do projeto real não foram definidas
> pelo grupo.

---

## Grupo e ponto de partida

- Grupo: Grupo 5 · Tratativa com o fornecedor
- Número a mover (baseline → meta): R$ 543 mil por mês pagos sem mercadoria, menos o recuperado (dossiê, Anexo 2) → a confirmar com o grupo
- Frente escolhida: Check antecipado e devolução, na parte da devolução ao fornecedor

## 1. Fases

| Fase | Objetivo | Entregável que o cliente vê | Duração | Critério de pronto |
| --- | --- | --- | --- | --- |
| Fase 0: acessos e dados | Liberar as decisões da doca, a leitura do ERP, o e-mail dos fornecedores e a decisão sobre emissão e hospedagem | Checklist de acessos com dono e status | a confirmar com o grupo | Cada acesso liberado ou com plano B combinado com o controller |
| Fase 1 | A devolução aprovada de E1, E2 e E3 entra na fila e é emitida pelo mock, sem duplicar | Fila "Notas de devolução" com status `emitida`, chave e número | a confirmar com o grupo | Emitir de novo a mesma devolução não gera outra nota (AC-4) |
| Fase 2 | O Claude prepara o e-mail; a pessoa revisa, emite e envia | Bloco "Pedido ao fornecedor" na gaveta, com a nota em PDF anexa | a confirmar com o grupo | Critérios de aceite da #156 passam; nada sai sem uma pessoa |
| Fase 3 | Ler a resposta do fornecedor e cobrar quem atrasou | Lista "Pedidos ao fornecedor" com aguardando, atendidos, precisam de você e atrasados | a confirmar com o grupo | Critérios de aceite da #157 passam |

O projeto tem oito semanas no total (briefing).

## 2. Primeiro valor

- O que é o primeiro valor que o cliente percebe: a primeira nota de devolução enviada ao fornecedor a partir de uma decisão de receber parcial, sem ninguém montar o texto.
- Em qual fase ele chega: Fase 2.
- Quanto tempo depois do início: a confirmar com o grupo (o contrato pede algo funcionando no primeiro mês, briefing).
- Quem no cliente percebe (papel): analista do contas a pagar e controller.
- Como o grupo vai mostrar que ele chegou: o pedido em `sent`, com a nota `emitida` na fila e o e-mail na caixa de saída.

## 3. Métricas

| Métrica | Tipo | Como medir | Baseline | Meta | Quando medir |
| --- | --- | --- | --- | --- | --- |
| Pago sem mercadoria, menos o recuperado (P1) | Resultado | Como no Anexo 2 do dossiê | R$ 543 mil por mês (jan–set) | a confirmar com o grupo | Mensal |
| Rascunhos descartados por número fora do catálogo (`number_mismatch`) | Qualidade do agente (eval) | Contar os descartes pelo sistema sobre os rascunhos escritos | Sem baseline (não existe hoje) | a confirmar com o grupo | a confirmar com o grupo |
| Pedidos enviados sobre pedidos abertos | Uso pela equipe do cliente | `sent` sobre o total de pedidos de E1, E2 e E3 | Sem baseline (não existe hoje) | a confirmar com o grupo | a confirmar com o grupo |
| Valor recuperado de fornecedores | Resultado intermediário | Devoluções atendidas pelo fornecedor, em reais | Zero em todos os meses (dossiê, Anexo 3) | a confirmar com o grupo | Mensal |

## 4. Dependências

| # | Dependência | De quem (papel ou sistema) | Necessária na fase | Se atrasar, o que acontece |
| --- | --- | --- | --- | --- |
| D1 | Decisões de receber parcial de E1, E2 e E3 (acesso A1) | Gerente de compras e comercial; a parte da doca da solução | Fase 1 | Sem decisão, não há pedido; a demo segue com a base do kit |
| D2 | E-mail de contato por fornecedor (acesso A4) | Gerente de compras e comercial | Fase 2 | O pedido não abre (`no_supplier_email`); segue o e-mail de teste por fornecedor |
| D3 | Emissão da nota no ERP (acesso A3) | Controller | Fase 2 | Segue o emissor mock; nada fiscal sai de verdade |
| D4 | Caixa de envio e de resposta (acessos A5 e A6) | Controller | Fases 2 e 3 | Segue a caixa mock; nenhum fornecedor recebe de verdade |
| D5 | Hospedagem com dados no Brasil e contrato de operador (acesso A7) | Controller e CFO | Fase 0 | Nada roda com dado real |

Os acessos do checklist (`checkpoint-2-access-checklist.md`) entram aqui como dependências.

## 5. Riscos do plano

| # | Risco | Fase afetada | Sinal de alerta | Resposta |
| --- | --- | --- | --- | --- |
| R1 | A escrita no ERP não sai nas oito semanas | Fase 2 | Homologação continua uma cópia de março | Entregar com o emissor mock e a nota pronta para o fiscal emitir |
| R2 | O jurídico não aceita devolução depois da entrada | Fases 2 e 3 | Fornecedor recusa as primeiras devoluções | Levar o caso ao controller; puxar o check antecipado |
| R3 | O volume estoura o teto do Worker | Fase 2 | Pedidos em `writing` acumulando por mais de duas rodadas | O passo vai para um Cron próprio (comentário em `api/src/cron/supplier-requests.ts`) |

## 6. Terminal Sul

- O que do plano se repete na Terminal Sul: a Fase 0 de acessos, o modo proposta, a porta de envio e o verificador de números.
- Em quanto tempo a Terminal Sul chegaria ao primeiro valor, reaproveitando o que foi feito: a confirmar com o grupo
- O que teria de ser feito de novo: a regra de quais decisões devolvem, os conectores dos sistemas dela e o cadastro de contatos.

## Conferência antes de entregar

- [ ] Toda fase tem entregável visível e critério de pronto.
- [ ] O primeiro valor tem fase, prazo e papel de quem percebe.
- [ ] O número a mover aparece nas métricas, com baseline e meta.
- [ ] Toda dependência tem dono e consequência de atraso.
