# Pitch de domingo

> Grupo 5 · Tratativa com o fornecedor. Pitch de domingo, 15/11, às 16h30.

---

## Grupo

- Grupo: Grupo 5 · Tratativa com o fornecedor
- Integrantes: Djegao (líder), pedroduff, augustotechrecruiter; demais a confirmar com o grupo

## Roteiro

| # | Bloco | Tempo | Quem fala |
| --- | --- | --- | --- |
| 1 | Problema | a definir pela coordenação | a confirmar com o grupo |
| 2 | Número | a definir pela coordenação | a confirmar com o grupo |
| 3 | Solução | a definir pela coordenação | a confirmar com o grupo |
| 4 | Demo | a definir pela coordenação | a confirmar com o grupo |
| 5 | Eval | a definir pela coordenação | a confirmar com o grupo |
| 6 | Próximos passos | a definir pela coordenação | a confirmar com o grupo |

### 1. Problema

- O problema da Já Chegou, em uma frase: a doca vê a divergência, mas ninguém pede ao fornecedor a devolução, e o dinheiro não volta.
- O pedido do CFO e como o grupo o reformulou: de "automatizar boleto e parar de pagar juros" para "toda divergência decidida vira nota de devolução e pedido ao fornecedor".
- Quem sente a dor (por papel): controller, analistas do contas a pagar e CFO.

### 2. Número

- A perda escolhida: pagar errado.
- Baseline: R$ 543 mil por mês pagos sem mercadoria; recuperado de fornecedores: zero (dossiê, Anexos 2 e 3).
- Meta: a confirmar com o grupo
- Por que este número: é cerca de 4,5 vezes os juros e multas (R$ 122 mil) que o CFO pediu para atacar.

### 3. Solução

- O fluxo proposto, em três a cinco passos:
  1. A pessoa decide receber parcial em E1, E2 ou E3.
  2. A regra monta a nota de devolução; o Claude escreve o e-mail, com números só do catálogo.
  3. O analista revisa e clica em "Emitir e enviar".
  4. A nota sai (mock) com chave e número, e o e-mail vai com o PDF anexo.
- Onde entra o agente: só no texto do e-mail; os números e os itens vêm da regra e do banco.
- Onde o humano decide (modo proposta e alçadas): nada é emitido nem enviado sem um clique; quem decide em cada alçada é pergunta ao cliente.

### 4. Demo

- O caso que a demo mostra: a página `demo-tratativa/` com a API falsa (`?mock=1`): rascunho, edição do texto, emitir e enviar.
- O que o público deve notar: os números e os itens ficam travados na edição; uma falha de envio reenviada não emite outra nota.
- Plano B se a demo falhar (gravação, capturas de tela): os cenários prontos da página (`cenario=writing`, `send-fails`, `system-discard`, `empty`); gravação ou capturas a confirmar com o grupo.

### 5. Eval

- Contra o que o agente foi avaliado (conjunto de casos com a resposta certa): a confirmar com o grupo. Não há eval do texto do agente com casos rotulados no código do grupo. Há testes automáticos: 133 testes em `api/test/supplier-requests/` e 7 da API falsa da demo, todos passando em 15/11, na branch `grupo-5/tratativa-fornecedor` (PR #40 do fork líder, versão alternativa da #156).
- Resultado: a confirmar com o grupo
- Onde o agente erra, e o que isso significa para o cliente: a confirmar com o grupo. Quando o texto traz um número fora do catálogo, o sistema descarta o rascunho e registra o motivo, e o analista não recebe um e-mail errado para enviar.

### 6. Próximos passos

- O que o grupo faria na próxima semana com o cliente: pedir a lista de e-mails dos fornecedores e a resposta sobre quem emite a nota de devolução; terminar a leitura da resposta e a cobrança (issue #157).
- O que se repete na Terminal Sul: pedido ao fornecedor com números verificados e decisão humana; muda a regra de quais decisões devolvem.
- O pedido ao cliente (decisão, acesso, patrocínio): decisão sobre a emissão da nota de devolução (ERP ou mock) e acesso ao cadastro de fornecedores.

## Conferência antes de subir ao palco

- [ ] Os seis blocos estão preenchidos, na ordem.
- [ ] Cada bloco tem quem fala.
- [ ] O número aparece com baseline e meta.
- [ ] A demo tem plano B.
- [ ] O resultado do eval aparece com os erros, não só com os acertos.
