# Fluxo IT-Support (resumo)

**Navegação:** [Índice](../INDICE.md) · [Fila](../FILA.md) · [Glossário](./GLOSSARIO.md) · [Modelo de trilha](./MODELO-TRILHA.md) · [Entrega](../ENTREGA.md)

Como o time de suporte costuma trabalhar tickets do projeto **IT** no Jira.

## 1. Chegada do ticket

- CX abre pelo **IT-Support** (Slack ou portal).
- O ticket cai no Jira com tipo **Support**, prioridade geralmente **Crítica**.
- Campos padrão: Problema, Nome, CPF, CCB, Solicitado por, Descrição.

## 2. Triagem

1. Ler a descrição e identificar a **categoria** (desembolso, pagamento, onboarding, etc.).
2. Buscar o cliente no **sistema interno** pelo CPF ou CCB.
3. Comparar o que o CX relatou com o **status real** no sistema.
4. Decidir: resolve no suporte, orienta o CX, ou escala para engenharia.

## 3. Resposta no ticket

Padrão observado nos tickets reais:

- Comentário curto com o que foi feito: *"Parcela baixada"*, *"Boleto gerado com vencimento 12/06"*, *"Termo reenviado"*.
- Mencionar quem abriu: `@agente.cx01` (handles fictícios neste teste).
- Anexar print do sistema interno quando fizer sentido (no teste, descreva o que anexaria).
- Mudar status para **EM ANÁLISE** enquanto investiga, **SOLUCIONADO** quando concluir.

## 4. Quando escalar

Escale quando:

- Status no sistema interno **contradiz** o relato e não há ação manual disponível
- Erro de sistema (botão que não funciona, API com erro repetido)
- Desembolso travado por falha do **bancarizador** sem opção de reprocessar
- Bug de onboarding que afeta vários clientes com o mesmo `error_code`

Não escale quando:

- Falta só **baixar pagamento** já identificado no extrato do Banco
- Gerar/cancelar boleto ou reenviar documento
- Cancelar CCB com motivo claro do bancarizador (indeferido)

## 5. Volume típico da fila

Nos últimos dias, a maior parte dos tickets IT-Support são:

1. **Desembolso ao cliente** — contrato assinado, cliente sem PIX
2. **Onboarding site** — erro ao continuar cadastro
3. **Pagamento do cliente** — PIX pago, parcela não baixada
4. **Erro ao gerar parcela/cobrança** — boleto errado (quitação vs parcela)
5. **Renegociação** — erro ao formalizar no Juvo Negocia
6. **Exclusão de cadastro** — LGPD / parar SMS

Este teste traz **12 tickets** cobrindo esse mix, incluindo duplicata do mesmo cliente, status inconsistente e dúvida de CX que pode ser rebaixada.
