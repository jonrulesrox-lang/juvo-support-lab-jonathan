# Índice — material do teste

Use esta página como **hub**. Todos os links abaixo funcionam no GitHub.

---

## Fluxo recomendado

| Fase | O que fazer |
|------|-------------|
| **1. Ler** | Glossário → Fluxo → Fila → tickets + CSV |
| **2. Resolver** | Priorizar fila e montar trilhas (ver [modelo](./docs/MODELO-TRILHA.md)) |
| **3. Entregar** | Criar `TRIAGEM.md` e `TRILHAS.md` na raiz; preencher [REVERSE.md](./docs/REVERSE.md) |

Tempo estimado: **~2h**. Foco principal: **trilha de troubleshooting** em cada ticket, não só a resposta final.

---

## Material de leitura (passos 1–6)

| Passo | Arquivo | Para quê |
|-------|---------|----------|
| 1 | [README — instruções gerais](./README.md) | Regras, prazo e visão do teste |
| 2 | [Glossário](./docs/GLOSSARIO.md) | Siglas e status (CCB, desembolso, baixa…) |
| 3 | [Fluxo IT-Support](./docs/FLUXO-IT-SUPPORT.md) | Como o suporte costuma atuar no Jira |
| 4 | [Fila do dia](./FILA.md) | Os 12 tickets da manhã + links |
| 5 | [Export do sistema interno](./data/sistema-interno-export.csv) | Dados para cruzar com cada ticket |
| 6 | [Modelo de trilha (exemplo)](./docs/MODELO-TRILHA.md) | Formato esperado em `TRILHAS.md` |

Detalhes do que entregar: [ENTREGA.md](./ENTREGA.md)

---

## Tickets (12)

Abra pela [FILA](./FILA.md) ou pelos links:

- [IT-1001 — Desembolso](./tickets/IT-1001.md)
- [IT-1002 — Onboarding](./tickets/IT-1002.md)
- [IT-1003 — Pagamento](./tickets/IT-1003.md)
- [IT-1004 — Erro parcela cobrança](./tickets/IT-1004.md)
- [IT-1005 — Erro negociação](./tickets/IT-1005.md)
- [IT-1006 — Exclusão de dados](./tickets/IT-1006.md)
- [IT-1007 — Termo de quitação](./tickets/IT-1007.md)
- [IT-1008 — Desembolso indeferido](./tickets/IT-1008.md)
- [IT-1009 — Desembolso / sync](./tickets/IT-1009.md)
- [IT-1010 — Parcela acelerada](./tickets/IT-1010.md)
- [IT-1011 — Desembolso (2ª CCB)](./tickets/IT-1011.md) *(mesmo CPF que IT-1008)*
- [IT-1012 — Dúvida prazo](./tickets/IT-1012.md)

---

## O que você entrega (passo 7)

| Arquivo | O que é | Quando |
|---------|---------|--------|
| `TRIAGEM.md` | Ordem dos 12 tickets + justificativa curta | Criar na **raiz** do repo |
| `TRILHAS.md` | Passo a passo do troubleshooting em **cada** ticket | Criar na **raiz** — **principal** |
| [docs/REVERSE.md](./docs/REVERSE.md) | Pergunta reversa + declaração de IA | **Preencher** o template já no repo |

### [Pergunta reversa + IA](./docs/REVERSE.md) — resumo

**Por quê:** no suporte sênior você precisa saber quando o ticket ou o dado **não fecha** e **o que perguntar** ao time antes de escalar. Esta parte avalia esse hábito.

**O que fazer:** escolha **um** ponto do teste (ticket, CSV ou ambiguidade), escreva a **pergunta que faria à Juvo** e explique **como a resposta mudaria sua trilha**. Depois declare se usou IA (permitido — seja transparente).

Instruções completas, exemplos e campos para preencher: **[docs/REVERSE.md](./docs/REVERSE.md)**
