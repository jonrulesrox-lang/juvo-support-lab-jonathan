# Modelo — trilha de troubleshooting

**Navegação:** [Índice](../INDICE.md) · [Fila](../FILA.md) · [Export CSV](../data/sistema-interno-export.csv) · [Entrega](../ENTREGA.md)

Para **cada** ticket, documente o **caminho que você seguiria na prática** — não só a resposta final.

Neste teste você não tem sistema interno, Banco (cobrança) nem fornecedor reais. Use o [export do sistema interno](../data/sistema-interno-export.csv) como se fosse a primeira consulta. Nos passos, deixe claro o que faria **no ambiente real** (sistema interno, consulta read-only em banco de dados, reprocesso, etc.).

---

## Esqueleto sugerido (adapte por caso)

```
1. Onde consulto primeiro     → sistema interno / export / logs / fila de desembolso…
2. O que busco                → CPF, CCB, status, último erro, pagamento…
3. O que encontrei            → evidência (campo do CSV neste teste)
4. Hipótese                   → causa provável em 1 linha
5. Ação — retry/reprocesso    → se aplicável; senão "não aplicável" + por quê
6. Ação — correção manual     → baixa, cancelar boleto, reenviar termo…
7. Escalação                  → dev / bancarizador / ninguém + contexto
8. Comunicação                → comentário que postaria no Jira (@agente)
9. Status final               → SOLUCIONADO / EM ANÁLISE / ESCALADO
```

Nem todo passo existe em todo ticket. **Pule com "N/A"** e explique em uma linha.

---

## Exemplo (IT-1003 — pagamento não baixado)

**1. Onde consulto primeiro**  
Sistema interno → contrato 80003001 / CPF 901.333.333-03 → aba parcelas e extrato do Banco.

**2. O que busco**  
Parcela 8 em aberto? Pagamento com mesmo valor/data do comprovante do CX?

**3. O que encontrei**  
CSV: `payment_status=pago`, `payment_id=banco_pay_fict_001`, R$ 403,06 em 10/06 17:32, mas `installment_status=aberta`.

**4. Hipótese**  
PIX compensado no Banco; webhook ou baixa automática não refletiu no sistema interno.

**5. Retry/reprocesso**  
Tentaria reprocessar webhook/baixa automática se existir botão ou job — uma vez.

**6. Correção manual**  
Se retry falhar ou não existir: **baixar parcela 8** manualmente no sistema interno vinculando ao `banco_pay_fict_001`.

**7. Escalação**  
N/A neste caso — pagamento identificado, ação operacional no suporte.  
Escala só se baixa manual falhar ou erro repetir em outros clientes.

**8. Comunicação**  
`@agente.cx03` Pagamento localizado no Banco (banco_pay_fict_001). Parcela 8 baixada no sistema interno. Pode orientar o cliente.

**9. Status final**  
SOLUCIONADO

---

Use este nível de detalhe nos 12 tickets em `TRILHAS.md` (ver [ENTREGA.md](../ENTREGA.md)).
