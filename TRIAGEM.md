# TRIAGEM.md — Ordem de atendimento IT-Support

## Critério de priorização

Como todos os tickets chegam com prioridade **Crítica** pelo formulário, reordenei a fila por impacto real:

1. **Movimentação financeira imediata** — pagamento já realizado, risco de cobrança indevida, boleto errado ou desembolso travado.
2. **Adesão ao serviço / jornada do cliente** — cliente aprovado ou em contratação, mas impedido de continuar.
3. **Falha sistêmica/inconsistência sem ação manual segura** — erro de integração, sync ou regra contraditória.
4. **Compliance e operações de suporte** — LGPD, documentos e solicitações sem impacto financeiro imediato.
5. **Dúvidas sem erro evidenciado** — orientar CX e rebaixar prioridade quando aplicável.

Os tickets **IT-1008** e **IT-1011** foram analisados em conjunto por terem o mesmo CPF, mas tratam **CCBs diferentes**.

## Observação metodológica

As decisões abaixo usam o `data/sistema-interno-export.csv` como evidência do teste. Em um sistema real, a mesma triagem seria validada no sistema interno, nas telas de contrato, parcelas, documentos, onboarding, fila de desembolso, logs e retorno de fornecedores, conforme o tipo de chamado.

| Ordem | Ticket | Classificação | Decisão | Resumo da triagem |
|---:|---|---|---|---|
| 1 | IT-1003 | P0 — financeiro/pagamento | Resolver no suporte | Cliente já pagou via PIX. Export mostra `payment_status=pago` e `installment_status=aberta`; risco de cobrança indevida. Reprocessar/baixar parcela 8. |
| 2 | IT-1004 | P0 — cobrança errada | Resolver no suporte | CX gerou quitação quando cliente queria pagar apenas a parcela 6. Cancelar documento de quitação e gerar cobrança correta da parcela. |
| 3 | IT-1010 | P0 — cobrança indevida | Resolver no suporte | Parcela acelerada gerou cobrança das parcelas 10 e 11, mas cliente deseja pagar somente a 10. Cancelar cobrança da 11 e manter a 10. |
| 4 | IT-1001 | P0 — desembolso travado | Retry e escalar se necessário | Contrato assinado, conta validada e desembolso pendente com `TIMEOUT_BANCARIZADOR`. Tentar reprocesso seguro; se não houver sucesso, escalar bancarizador/integração. |
| 5 | IT-1009 | P0 — inconsistência financeira | Escalar para Engenharia/Integração | Export contraditório: `contract_status=assinatura_pendente`, mas `disbursement_status=aguardando_desembolso`. Não há ação manual segura. |
| 6 | IT-1002 | P1 — adesão/onboarding | Escalar para Onboarding/KYC | Cliente aprovado no Alpha9 e conta validada, mas travado na etapa `bank_account` com `KYC_BANK_MISMATCH`. Afeta entrada no serviço. |
| 7 | IT-1005 | P1 — renegociação/regularização | Escalar para Juvo Negocia/Engenharia | Contrato elegível com 112 dias de atraso, mas bloqueado por `FLAG_ACCELERATED_BLOCK`; relato diz que não há parcelas aceleradas. |
| 8 | IT-1008 | P2 — desembolso indeferido | Orientar CX / solucionar | CCB 80008001 está cancelada e desembolso `indeferido`; há motivo claro. Não reprocessar. Validar junto com IT-1011 por mesmo CPF. |
| 9 | IT-1011 | P2 — desembolso em andamento | Acompanhar / orientar CX | Mesmo CPF do IT-1008, mas CCB 80008002 está assinada e `aguardando_desembolso` sem erro. Não escalar antes do SLA/runbook. |
| 10 | IT-1006 | P2 — LGPD/compliance | Escalar para Privacidade/LGPD + ação operacional | Solicitação de exclusão/parar SMS. Export indica `lgpd_delete_requested=sim`, porém `lead_status=lead_ativo`; precisa garantir opt-out e fluxo LGPD. |
| 11 | IT-1007 | P3 — documento pós-quitação | Resolver no suporte | Contrato já `quitado`, mas `term_email_status=falha_envio`. Reenviar/reprocessar termo de quitação. |
| 12 | IT-1012 | P4 — dúvida de prazo | Orientar CX / rebaixar prioridade | Assinou há ~3h, conta validada e `aguardando_desembolso` sem erro. Não há incidente evidenciado; orientar prazo padrão. |
