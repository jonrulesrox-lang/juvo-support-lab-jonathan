# TRILHAS.md — Troubleshooting IT-Support

## Critério usado para ordenar a fila

Como todos os chamados chegaram no Jira como **Crítica**, a fila foi reordenada por impacto real de atendimento:

1. **Movimentação financeira imediata:** pagamento já realizado, risco de cobrança indevida, boleto errado ou desembolso travado. Na minha visão devemos priorizar fluxos de financeiros.
2. **Adesão ao serviço:** cliente aprovado ou em contratação, mas impedido de continuar a jornada.
3. **Inconsistência sistêmica sem ação manual segura:** divergência entre status, sync ou regra de produto.
4. **Compliance e operação:** LGPD, reenvio de documento e ajustes sem risco financeiro imediato.
5. **Dúvida sem erro evidenciado:** orientar CX e rebaixar prioridade.

## Observação metodológica

Neste teste, como não tenho acesso ao sistema interno real, Banco, bancarizador, logs ou fornecedor, estou me baseando apenas no export `data/sistema-interno-export.csv` como a primeira consulta/evidência disponível.  
Em um ambiente real, eu validaria os mesmos pontos no sistema interno, em telas operacionais como contrato, parcelas, documentos, fila de desembolso, logs, extrato do provedor Banco, retorno do bancarizador e histórico de eventos/auditoria, conforme o tipo de chamado. Também veria a parte de validação financeira, se teria alguma tela ou banco de dados para confirmar as moviemntações financeiras e em cima disso tornar as analises mais efetivas e evidenciadas para segurança nas ações de baixas e trativas financeiras. Em alguns casos tbm, eu realizaria validações dos serviços conforme levantamento de dados em cenários parecidos, por exemplo, em caso de um pagamento que está sendo realizado e a baixa não está refletindo corretamente no sistema, eu iniciaria fazendo um levantando de casos que atenderiam o mesmo critério e validaria se não é algum problema interno, alguma api fora, algum job que não está executando corretamente, faria se necessário debug na aplicação para encontrar incosistência, validaria questões de deploys para entender se não foi realizada alguma mudança que possa ter gerado alguma alteração no serviço, replicaria o cenário tbm para complementar a validação, após analises seguiria com o acionamento dos times responsáveis para tratativas. 

obs: faria os direcionamentos e passaria as informações para minha coordenação/gestão, deixando informado sobre a situação. 

---

## IT-1003 — Pagamento do cliente

**Prioridade na fila:** 1 — P0 financeiro. Cliente já pagou via PIX e a parcela continua aberta, gerando risco de cobrança indevida.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90133333303` ou CCB `80003001`. No sistema real, abriria o contrato no sistema interno, validaria a tela de parcelas, o histórico de baixa e o extrato/retorno do provedor Banco, validaria através da base de dados tbm e se ncessário validaria o recebimento com a área financeira. 
2. **O que busco:** se a parcela informada pelo CX está aberta, se existe pagamento PIX identificado, valor, data/hora, provedor e ID do pagamento.
3. **O que encontrei:** CSV: `installment_ref=parcela_8`, `installment_status=aberta`, `installment_amount_brl=403.06`, `payment_provider=banco`, `payment_id=banco_pay_fict_001`, `payment_status=pago`, `payment_amount_brl=403.06`, `payment_received_at=2026-06-10T17:32:00Z`.
4. **Hipótese:** pagamento compensado no Banco, mas webhook/job de baixa automática não refletiu a parcela no sistema interno.
5. **Retry / reprocesso:** tentaria reprocessar a baixa/webhook uma vez, caso exista ação operacional segura no sistema. Antes, validaria se o pagamento ainda não foi baixado por outro processo para evitar duplicidade.
6. **Correção manual no sistema interno:** se o retry não existir ou não surtir efeito, baixar manualmente a parcela 8 vinculando ao `banco_pay_fict_001`, registrando evidência do pagamento.Deixaria também o caso em atenção e validaria se temos cenários semelhantes para fazer uma validação de ambiente e entender se não é algo que está afetando o serviço, importante fazer esta ação afins de garantir o troubleshooting completo. 
7. **Escalação:** N/A inicialmente. Não escalar porque o pagamento está identificado e a baixa é ação operacional do suporte. Escalar somente se a baixa manual falhar ou se houver recorrência em massa.
8. **Comunicação no Jira:** `@agente.cx03` Pagamento localizado no Banco (`banco_pay_fict_001`, R$ 403,06, recebido em 10/06 17:32). Parcela 8 baixada no sistema interno. Anexaria print da parcela aberta antes da baixa e do ID de pagamento no extrato.
9. **Status final:** SOLUCIONADO.

---

## IT-1004 — Erro ao gerar parcela(s) cobrança (sistema interno)

**Prioridade na fila:** 2 — P0 cobrança errada. Foi gerado documento de quitação quando o cliente queria pagar apenas uma parcela.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90144444404` ou CCB `80004001`. No sistema real, abriria o contrato no sistema interno, validaria parcelas em aberto, documentos/cobranças ativas e histórico de geração de boleto/PIX.
2. **O que busco:** se existe boleto/PIX de quitação ativo, se a parcela 6 está aberta, qual documento foi gerado e se é possível cancelar a cobrança incorreta.
3. **O que encontrei:** CSV: `contract_status=ativo`, `installment_ref=parcela_6`, `installment_status=aberta`, `installment_amount_brl=403.06`, `last_doc_generated=doc_fict_0401`, `last_doc_type=quitacao`, `last_doc_status=ativa`.
4. **Hipótese:** atendente gerou cobrança de quitação total por engano no lugar do boleto/PIX da parcela 6.
5. **Retry / reprocesso:** N/A. Não é falha de processamento; é documento incorreto ativo.
6. **Correção manual no sistema interno:** cancelar/inativar o documento de quitação `doc_fict_0401` e gerar a cobrança correta da `parcela_6` no valor de R$ 403,06.
7. **Escalação:** N/A. Gerar/cancelar boleto ou documento de cobrança com motivo claro é ação operacional do suporte.
8. **Comunicação no Jira:** `@agente.cx04` Documento de quitação gerado por engano foi cancelado e a cobrança correta da parcela 6 foi gerada no valor de R$ 403,06. Anexaria print do documento cancelado e da nova cobrança da parcela 6.
9. **Status final:** SOLUCIONADO.

---

## IT-1010 — Erro ao gerar parcela(s) cobrança (sistema interno)

**Prioridade na fila:** 3 — P0 cobrança indevida. Cliente deseja pagar apenas a parcela 10, mas a cobrança foi acelerada com parcela 11.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90110101010` ou CCB `80010001`. No sistema real, abriria a tela de parcelas, documentos ativos e histórico de parcela acelerada no sistema interno.
2. **O que busco:** quais parcelas foram aceleradas, quais documentos estão ativos e se é possível cancelar somente a parcela que o cliente não deseja pagar.
3. **O que encontrei:** CSV: `contract_status=ativo`, `installment_ref=parcela_10;parcela_11`, `installment_status=aberta`, `installment_amount_brl=380.00`, `last_doc_generated=doc_acc_fict_10;doc_acc_fict_11`, `last_doc_type=parcela_acelerada`, `last_doc_status=ativa`, `accelerated_installments=10;11`.
4. **Hipótese:** cobrança foi gerada como parcela acelerada para as parcelas 10 e 11, mas a intenção correta era manter somente a parcela 10.
5. **Retry / reprocesso:** N/A. Não é caso de retry; é ajuste/cancelamento de documento de cobrança.
6. **Correção manual no sistema interno:** cancelar o documento/cobrança referente à parcela 11 (`doc_acc_fict_11`) e manter ou regerar somente a cobrança da parcela 10 (`doc_acc_fict_10`), validando vencimento e valor final antes de orientar o CX.
7. **Escalação:** N/A inicialmente. Escalar apenas se o sistema não permitir separar/cancelar a parcela 11 sem afetar a parcela 10.
8. **Comunicação no Jira:** `@agente.cx09` Cobrança acelerada revisada. Parcela 11 cancelada/removida da cobrança, mantendo somente a parcela 10 para pagamento. Anexaria print das parcelas aceleradas antes/depois da correção.
9. **Status final:** SOLUCIONADO.

---

## IT-1001 — Desembolso ao cliente

**Prioridade na fila:** 4 — P0 desembolso travado. Cliente assinou, conta está validada e há falha no bancarizador.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90111111101` ou CCB `80001001`. No sistema real, abriria o contrato no sistema interno, a fila de desembolso, logs de integração e retorno do bancarizador.
2. **O que busco:** status do contrato, status de desembolso, conta validada, última tentativa, último erro do bancarizador e evidência de que não houve PIX enviado para evitar duplicidade.
3. **O que encontrei:** CSV: `contract_status=assinado`, `disbursement_status=aguardando_desembolso`, `disbursement_error=TIMEOUT_BANCARIZADOR`, `last_disbursement_attempt=2026-06-11T06:45:00Z`, `bank_validated=sim`, `credit_status=aprovado_alpha9`, `hours_since_signature=18`.
4. **Hipótese:** contrato elegível para desembolso, mas a tentativa de PIX ficou travada por timeout do bancarizador externo.
5. **Retry / reprocesso:** tentaria um único reprocesso seguro do desembolso, se existir botão/job autorizado no runbook. Antes do retry, validaria se não existe pagamento já confirmado pelo bancarizador.
6. **Correção manual no sistema interno:** N/A. Não alterar manualmente status financeiro nem marcar como desembolsado sem confirmação do bancarizador.
7. **Escalação:** escalar para Engenharia de Integração/Bancarizador se não houver opção segura de retry ou se o retry retornar timeout novamente. Enviar CCB, CPF, horário da última tentativa e erro `TIMEOUT_BANCARIZADOR`.
8. **Comunicação no Jira:** `@agente.cx01` Contrato assinado e conta validada, porém o desembolso está aguardando retorno do bancarizador com erro `TIMEOUT_BANCARIZADOR` na última tentativa. Reprocesso validado/solicitado e caso escalado para integração/bancarizador. Anexaria print do status de desembolso e erro.
9. **Status final:** ESCALADO.

---

## IT-1009 — Desembolso ao cliente

**Prioridade na fila:** 5 — P0 inconsistência financeira. Export mostra assinatura pendente, mas desembolso aguardando execução.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90199999909` ou CCB `80009001`. No sistema real, consultaria tela de contrato, eventos de assinatura, fila de desembolso e logs de sincronismo entre assinatura e motor financeiro.
2. **O que busco:** se o contrato está realmente assinado, se a conta foi validada, se existe evento de assinatura aceito e por que o desembolso foi enfileirado.
3. **O que encontrei:** CSV: `contract_status=assinatura_pendente`, `disbursement_status=aguardando_desembolso`, `disbursement_error=SYNC_STATUS_MISMATCH`, `last_disbursement_attempt=2026-06-11T09:00:00Z`, `bank_validated=sim`.
4. **Hipótese:** inconsistência de sincronismo entre assinatura/contrato e motor de desembolso. O fluxo financeiro foi enfileirado sem o contrato estar refletido como assinado.
5. **Retry / reprocesso:** N/A. Não reprocessar desembolso enquanto o status do contrato estiver contraditório.
6. **Correção manual no sistema interno:** N/A. Não corrigir manualmente contrato para assinado nem forçar desembolso sem evidência da assinatura válida.
7. **Escalação:** escalar para Engenharia de Integração/Sincronismo com evidência do `SYNC_STATUS_MISMATCH`, porque há risco financeiro e não há ação manual segura.
8. **Comunicação no Jira:** `@agente.cx08` Identificada inconsistência: contrato aparece como `assinatura_pendente`, mas desembolso está `aguardando_desembolso` com erro `SYNC_STATUS_MISMATCH`. Por segurança, não realizei reprocesso manual e escalei para Engenharia validar assinatura/sync antes de qualquer desembolso. Anexaria print dos status contraditórios.
9. **Status final:** ESCALADO.

---

## IT-1002 — Onboarding site (Continuar cadastro pela área logada)

**Prioridade na fila:** 6 — P1 adesão ao serviço. Cliente aprovado no Alpha9 não consegue continuar cadastro.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90122222202`. No sistema real, abriria o cadastro/lead no sistema interno, a jornada de onboarding, logs web/app, eventos KYC e validação bancária.
2. **O que busco:** etapa atual do onboarding, status de crédito, conta validada, erro de KYC/onboarding e se o mesmo `error_code` ocorre com outros clientes.
3. **O que encontrei:** CSV: `bank_validated=sim`, `onboarding_step=bank_account`, `onboarding_error=KYC_BANK_MISMATCH`, `credit_status=aprovado_alpha9`, `lead_status=lead_ativo`.
4. **Hipótese:** cliente está aprovado no Alpha9, mas a jornada trava na etapa de conta bancária por divergência de KYC/conta, apesar de `bank_validated=sim`.
5. **Retry / reprocesso:** N/A inicialmente. O cliente já tentou app, site e outro dispositivo; repetir tentativa pelo CX tende a não resolver. No sistema real, validaria se existe revalidação KYC segura antes de qualquer reprocesso.
6. **Correção manual no sistema interno:** N/A sem runbook específico. Não avançar etapa manualmente sem validar KYC/conta, pois envolve segurança cadastral.
7. **Escalação:** escalar para Onboarding/KYC ou Engenharia com `onboarding_error=KYC_BANK_MISMATCH`, etapa `bank_account`, CPF e evidência de conta validada.
8. **Comunicação no Jira:** `@agente.cx02` Cliente aprovado no Alpha9, porém travado na etapa `bank_account` com erro `KYC_BANK_MISMATCH`, mesmo com conta validada. Como já houve tentativa em app/site/outro dispositivo, escalei para Onboarding/KYC validar a divergência. Anexaria print da etapa atual e do erro.
9. **Status final:** ESCALADO.

---

## IT-1005 — Erro ao gerar negociação (Juvo Negocia)

**Prioridade na fila:** 7 — P1 renegociação/regularização. Cliente elegível e em atraso, mas não consegue formalizar negociação.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90155555505` ou CCB `80005001`. No sistema real, abriria contrato, parcelas em atraso, elegibilidade no Juvo Negocia, regras de bloqueio e histórico de parcelas aceleradas.
2. **O que busco:** dias em atraso, elegibilidade para renegociação, motivo de bloqueio, existência de parcelas aceleradas e erro retornado no fluxo Juvo Negocia.
3. **O que encontrei:** CSV: `contract_status=ativo`, `renegotiation_eligible=sim`, `renegotiation_block_reason=FLAG_ACCELERATED_BLOCK`, `days_past_due=112`. O ticket informa que o contrato está dentro dos critérios e sem parcelas aceleradas.
4. **Hipótese:** regra/flag de bloqueio de renegociação está inconsistente. O sistema bloqueia por `FLAG_ACCELERATED_BLOCK`, apesar do relato indicar ausência de parcelas aceleradas.
5. **Retry / reprocesso:** N/A. Não há evidência de falha transitória; é validação de regra/flag.
6. **Correção manual no sistema interno:** N/A sem autorização. Não remover flag de bloqueio manualmente sem validação de regra de negócio, pois pode gerar proposta incorreta.
7. **Escalação:** escalar para Engenharia/Produto Juvo Negocia com CCB, dias em atraso, elegibilidade e motivo de bloqueio. Solicitar validação da flag e correção se for inconsistência.
8. **Comunicação no Jira:** `@agente.cx03` Contrato consta elegível para renegociação (`renegotiation_eligible=sim`) e com 112 dias de atraso, porém o Juvo Negocia bloqueia por `FLAG_ACCELERATED_BLOCK`. Escalei para validação da regra/flag antes de formalizar proposta. Anexaria print da elegibilidade e do erro.
9. **Status final:** ESCALADO.

---

## IT-1008 — Desembolso ao cliente

**Prioridade na fila:** 8 — P2 análise agrupada por CPF. Mesmo cliente do IT-1011, mas CCB diferente e com indeferimento/cancelamento claro.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90188888808` e comparo todas as CCBs do CPF. No sistema real, abriria a visão consolidada do cliente, histórico de contratos/CCBs e retorno do bancarizador para cada CCB.
2. **O que busco:** status da CCB 80008001, motivo do desembolso indeferido, status do contrato e existência de outra CCB ativa do mesmo cliente.
3. **O que encontrei:** CSV para CCB `80008001`: `contract_status=cancelado`, `disbursement_status=indeferido`, `disbursement_error=BANCARIZADOR_OK`, `last_disbursement_attempt=2026-06-11T08:10:00Z`, `bank_validated=sim`. No mesmo CPF existe também a CCB `80008002`, tratada no IT-1011.
4. **Hipótese:** CX misturou o histórico do mesmo CPF. A CCB 80008001 foi encerrada/cancelada com desembolso indeferido, enquanto outra CCB está em andamento.
5. **Retry / reprocesso:** N/A. Não reprocessar desembolso de contrato cancelado/indeferido.
6. **Correção manual no sistema interno:** N/A. Como há motivo/status claro e contrato cancelado, não há ajuste operacional a fazer nessa CCB.
7. **Escalação:** N/A. Não escalar quando há motivo claro de indeferimento/cancelamento. Orientar CX considerando também a existência da CCB 80008002.
8. **Comunicação no Jira:** `@agente.cx07` Para a CCB 80008001, o contrato consta como cancelado e desembolso indeferido. Não há reprocesso nessa CCB. Identifiquei outra CCB do mesmo CPF em andamento, relacionada ao IT-1011. Anexaria print das duas CCBs para evitar confusão no atendimento.
9. **Status final:** SOLUCIONADO.

---

## IT-1011 — Desembolso ao cliente

**Prioridade na fila:** 9 — P2 acompanhamento de desembolso. Mesmo CPF do IT-1008, mas CCB diferente e sem erro no export.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90188888808` e CCB `80008002`, comparando com a CCB `80008001` do IT-1008. No sistema real, validaria a visão do cliente, fila de desembolso, histórico de tentativas e SLA/runbook de desembolso.
2. **O que busco:** se a CCB correta está assinada, se a conta está validada, status de desembolso, última tentativa, erro registrado e tempo desde assinatura.
3. **O que encontrei:** CSV para CCB `80008002`: `contract_status=assinado`, `disbursement_status=aguardando_desembolso`, `disbursement_error` vazio, `last_disbursement_attempt=2026-06-10T22:00:00Z`, `bank_validated=sim`, `hours_since_signature=14`. A CCB 80008001 do mesmo CPF está cancelada/indeferida.
4. **Hipótese:** a nova CCB está em fila normal de desembolso, sem erro técnico registrado; o ticket pode ter sido aberto por confusão com a CCB anterior indeferida.
5. **Retry / reprocesso:** N/A por enquanto. Sem erro de bancarizador ou status inconsistente, não reprocessar para evitar duplicidade.
6. **Correção manual no sistema interno:** N/A. Não há pagamento a baixar nem cobrança a cancelar.
7. **Escalação:** não escalar inicialmente. Manter em acompanhamento se o SLA de desembolso ainda não foi ultrapassado; escalar apenas se exceder SLA ou surgir erro na fila.
8. **Comunicação no Jira:** `@agente.cx07` Verifiquei as duas CCBs do CPF. A CCB 80008001 está cancelada/indeferida, mas a CCB 80008002 está assinada, conta validada e aguardando desembolso, sem erro registrado. Manter acompanhamento dentro do SLA; se exceder prazo ou retornar erro, escalar para integração. Anexaria print comparando as CCBs.
9. **Status final:** EM ANÁLISE.

---

## IT-1006 — Exclusão de cadastro/Dados

**Prioridade na fila:** 10 — P2 compliance/LGPD. Não tem impacto financeiro imediato, mas exige tratativa formal e rastreável.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90166666606`. No sistema real, abriria cadastro/lead, consentimentos de comunicação, opt-out de SMS, solicitações LGPD e histórico de atendimento.
2. **O que busco:** se há contrato/CCB, status do lead, pedido de exclusão, opt-out de SMS e se existe fluxo formal de privacidade já aberto.
3. **O que encontrei:** CSV: `ccb` vazio, `bank_validated=nao`, `onboarding_step=completed`, `credit_status=reprovado_alpha9`, `lead_status=lead_ativo`, `lgpd_delete_requested=sim`.
4. **Hipótese:** pedido de exclusão/privacidade já foi registrado, mas o lead ainda está ativo e pode continuar recebendo comunicação.
5. **Retry / reprocesso:** N/A para retry técnico. Ação aplicável é garantir bloqueio de comunicação e encaminhamento do processo LGPD.
6. **Correção manual no sistema interno:** se permitido pelo runbook, aplicar opt-out/parar SMS e inativar lead. Não excluir dados diretamente sem processo formal de privacidade.
7. **Escalação:** escalar para Privacidade/LGPD ou backoffice responsável por exclusão definitiva, com evidência de `lgpd_delete_requested=sim` e `lead_status=lead_ativo`.
8. **Comunicação no Jira:** `@agente.cx05` Solicitação LGPD localizada (`lgpd_delete_requested=sim`). Lead/comunicações serão bloqueados conforme fluxo e caso encaminhado ao time responsável pela exclusão definitiva. Anexaria print do cadastro, status do lead e flag LGPD.
9. **Status final:** ESCALADO.

---

## IT-1007 — Erro ou demora na emissão de Termo de quitação

**Prioridade na fila:** 11 — P3 operação/documento. Contrato já está quitado; pendência é reenvio do termo.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90177777707` ou CCB `80007001`. No sistema real, abriria contrato, documentos gerados, histórico de envio de e-mail e logs do serviço de notificação.
2. **O que busco:** se o contrato está quitado, se o termo foi gerado, status de envio e e-mail cadastrado.
3. **O que encontrei:** CSV: `contract_status=quitado`, `bank_validated=sim`, `term_email_status=falha_envio`. No ticket consta e-mail `cliente.ficticio.g@exemplo.test`.
4. **Hipótese:** contrato está quitado, mas houve falha no envio automático do termo por e-mail.
5. **Retry / reprocesso:** reprocessar envio do termo para o e-mail cadastrado; se houver opção, gerar novamente o documento antes do envio.
6. **Correção manual no sistema interno:** reenviar termo de quitação ou anexar/registrar envio manual conforme runbook.
7. **Escalação:** N/A inicialmente. Reenviar documento é ação operacional do suporte. Escalar apenas se o gerador de documento ou serviço de e-mail falhar novamente.
8. **Comunicação no Jira:** `@agente.cx06` Contrato consta como quitado. Identifiquei falha no envio do termo e realizei novo envio para o e-mail cadastrado. Anexaria print do contrato quitado e do status de reenvio do termo.
9. **Status final:** SOLUCIONADO.

---

## IT-1012 — Dúvida — prazo de desembolso

**Prioridade na fila:** 12 — P4 dúvida sem erro evidenciado. Cliente assinou há poucas horas e não há falha no export.

### Trilha de troubleshooting

1. **Onde consulto primeiro:** neste teste, consulto o `data/sistema-interno-export.csv` pelo CPF `90112121212` ou CCB `80012001`. No sistema real, abriria o contrato, fila de desembolso e histórico de tentativas apenas para confirmar status e prazo.
2. **O que busco:** status do contrato, status do desembolso, validação de conta, último erro e tempo desde assinatura.
3. **O que encontrei:** CSV: `contract_status=assinado`, `disbursement_status=aguardando_desembolso`, `disbursement_error` vazio, `last_disbursement_attempt=2026-06-11T11:30:00Z`, `bank_validated=sim`, `hours_since_signature=3`.
4. **Hipótese:** não há incidente técnico; desembolso está em fila dentro do prazo esperado, dependendo do SLA oficial.
5. **Retry / reprocesso:** N/A. Sem erro e com apenas 3h desde a assinatura, não há motivo para reprocessar.
6. **Correção manual no sistema interno:** N/A. Não há cobrança, pagamento ou documento a ajustar.
7. **Escalação:** N/A. Só escalar se o SLA oficial for ultrapassado ou se surgir erro de bancarizador/sync.
8. **Comunicação no Jira:** `@agente.cx10` Contrato assinado, conta validada e desembolso aguardando processamento, sem erro registrado. Cliente assinou há aproximadamente 3h; pode orientar o prazo padrão de desembolso e retornar se exceder o SLA. Anexaria print do status `aguardando_desembolso` sem erro.
9. **Status final:** SOLUCIONADO.
