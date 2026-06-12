# ESCALACOES.md — Resumo de tickets escalados


> Observação: as escalações abaixo usam o `data/sistema-interno-export.csv` como evidência do teste. Em ambiente real, eu anexaria prints/logs do sistema interno, fila de desembolso, retorno do bancarizador, onboarding/KYC, Juvo Negocia ou fila LGPD, conforme a necessidade.


| Ticket | Destino | Título sugerido | Contexto |
|---|---|---|---|
| IT-1001 | Engenharia de Integração + Bancarizador | Desembolso travado por `TIMEOUT_BANCARIZADOR` | CCB 80001001 está assinada, conta validada e aguardando desembolso. Última tentativa retornou timeout do bancarizador. Escalar se não houver botão seguro de reprocesso ou se retry falhar. |
| IT-1009 | Engenharia de Integração / Sincronismo | `SYNC_STATUS_MISMATCH` entre assinatura e desembolso | Contrato consta como `assinatura_pendente`, mas desembolso está `aguardando_desembolso`. Não é seguro reprocessar ou alterar manualmente antes de corrigir o sync. |
| IT-1002 | Onboarding/KYC / Engenharia | Cliente aprovado travado em `KYC_BANK_MISMATCH` | Lead aprovado no Alpha9, `bank_validated=sim`, mas onboarding trava na etapa `bank_account`. Cliente testou app, site e outro dispositivo. |
| IT-1005 | Juvo Negocia / Engenharia de Produto | Renegociação elegível bloqueada por flag acelerada | Contrato com 112 dias de atraso e `renegotiation_eligible=sim`, porém bloqueado por `FLAG_ACCELERATED_BLOCK`, contradizendo relato de ausência de parcelas aceleradas. |
| IT-1006 | Privacidade/LGPD / Backoffice responsável | Solicitação LGPD com lead ainda ativo | Cliente solicita exclusão/parar SMS. Export indica `lgpd_delete_requested=sim`, mas `lead_status=lead_ativo`; precisa confirmar opt-out/inativação e execução do fluxo LGPD. |
