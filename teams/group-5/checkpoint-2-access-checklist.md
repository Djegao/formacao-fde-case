# Checkpoint 2: checklist de acessos, credenciais e dados

> Grupo 5 · Tratativa com o fornecedor. Ponto de partida: dossiê, Anexo 6. O que a solução usa
> hoje no lugar do acesso real está na coluna "Plano B" e no código da issue #156.

---

## Grupo

- Grupo: Grupo 5 · Tratativa com o fornecedor

## Acessos

| # | Sistema ou fonte | O que precisamos | Tipo de acesso | Para quê | Dono (papel) | Prazo | Risco (nível e motivo) | Plano B | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| A1 | Decisões da doca (WMS do CD e app de loja, pela solução do caso) | As propostas decididas como receber parcial em E1, E2 e E3, com as linhas devolvidas | Leitura | Abrir o pedido ao fornecedor e montar a nota de devolução | Gerente de compras e comercial | a confirmar com o grupo | Médio: hoje a divergência da loja corre por WhatsApp e o WMS só tem relatório diário | Base do kit e as decisões gravadas no Supabase da solução | Pendente (não pedido; dossiê, Anexo 6) |
| A2 | ERP, produção | Notas lançadas, títulos e fornecedores | Leitura | Ligar a devolução à nota de origem e ao título | Controller | a confirmar com o grupo | Médio: dado fiscal e financeiro; exige termo de confidencialidade por pessoa | Exportação diária de títulos (CSV por e-mail) e a base do kit | Pedido, sem previsão (dossiê, Anexo 6) |
| A3 | ERP | Emitir a nota de devolução | Escrita | Passo "Emitir e enviar": a nota sai com chave e número | Controller | a confirmar com o grupo | Alto: nota fiscal emitida errada; nada escreve sem homologação, e a homologação é uma cópia de março | Emissor mock no formato das notas do kit (chave de 44 dígitos e número), sem chamar serviço fiscal | Pendente (não pedido; dossiê, Anexo 6) |
| A4 | Cadastro de fornecedores | E-mail de contato de cada fornecedor | Leitura | Endereço do e-mail ao fornecedor | Gerente de compras e comercial | a confirmar com o grupo | Médio: fornecedor pessoa física tem dado pessoal | Tabela `supplier_contacts` com um e-mail de teste por fornecedor, gravada por migração | Pendente (não pedido) |
| A5 | Envio de e-mail (caixa do contas a pagar ou Resend) | Enviar o e-mail com a nota anexa | Escrita (envio) | Entregar o pedido ao fornecedor | Controller | a confirmar com o grupo | Alto: e-mail enviado ao fornecedor errado ou com número errado | Caixa de saída mock no banco (`outbox_supplier_emails`); o Resend é opcional | Pendente (não pedido) |
| A6 | Caixa de e-mail do contas a pagar | Respostas dos fornecedores aos pedidos | Leitura | Ler a resposta e preparar a cobrança (issue #157) | Controller | a confirmar com o grupo | Alto: tem dados pessoais de fornecedores pessoa física; o texto do fornecedor pode trazer ordens ao agente | Caixa de entrada mock com respostas de exemplo (`inbox_supplier_messages`, issue #157) | Pendente (não pedido; dossiê, Anexo 6) |
| A7 | Onde a solução roda | Hospedar os dados fiscais da solução | Hospedagem | Banco dos pedidos, das notas emitidas e dos e-mails | Controller e CFO | a confirmar com o grupo | Médio: o controller exige dados fiscais no Brasil e contrato de operador de dados | Supabase em São Paulo, como na arquitetura de referência | Em aberto (dossiê, Anexo 6) |

## Credenciais: como vão ser guardadas

- Onde as credenciais ficam (nunca no repositório; por exemplo, no arquivo `.env`, que não vai para o Git): em secrets do Worker. O acesso ao Claude usa o setup token já configurado no Worker (`BARTE_ACADEMY_CLAUDE_CODE_OAUTH_TOKEN`, posto pelo `deploy-api.yml`). A chave do Resend, se usada, fica em `RESEND_API_KEY`, também secret, nunca no git (issue #156, AC-5).
- Quem do grupo tem acesso a elas: a confirmar com o grupo
- Quando e como serão revogadas ao fim do trabalho: a confirmar com o grupo

## Dados sensíveis

- Quais dados pessoais ou financeiros passam pela solução: e-mail do fornecedor (pessoa física em alguns casos), notas fiscais, valores devolvidos e a nota de devolução.
- O que o grupo faz para não expô-los (mascarar, não registrar em log, não versionar):
  - o log do Cron conta só o motivo e a quantidade dos casos, sem chave de nota nem e-mail (`api/src/cron/supplier-requests.ts`);
  - a falha de envio guarda um código curto (`send_error`), nunca o texto do provedor;
  - nenhum erro da API leva o texto da dependência; o detalhe vai ao log com um id de correlação (`api/src/http/supplier-requests.ts`);
  - a chave do Resend entra na lista de segredos que o log nunca mostra.

## Perguntas para o cliente

| # | Pergunta | Para quem (papel) |
| --- | --- | --- |
| P1 | A nota de devolução pode ser emitida por nós, ou só pelo time fiscal no ERP? Existe homologação atualizada? | Controller |
| P2 | Podemos receber uma lista de e-mail de contato fiscal por fornecedor? | Gerente de compras e comercial |
| P3 | Por qual caixa o e-mail ao fornecedor deve sair, e quem responde a ele? | Controller |

## Conferência antes de entregar

- [ ] Toda linha tem dono por papel, prazo, risco e plano B.
- [ ] Nenhum acesso de escrita sem motivo explícito na coluna "Para quê".
- [ ] Nenhuma credencial real escrita neste arquivo.
- [ ] Cada acesso aparece no desenho da solução (`checkpoint-2-solution-design.md`).
