# Juvo — Teste técnico · Analista Sênior de Suporte

**→ [Abrir índice com todos os links](./INDICE.md)**

---

## Cenário

Você assumiu a fila do **IT-Support** numa manhã de segunda-feira, **11/06/2026**.

> **Aviso:** todos os nomes, CPFs, CCBs, e-mails e telefones são **100% fictícios**. O cenário imita o formato real do IT-Support, mas não usa dados de clientes reais.

O time de CX abriu **12 tickets** pelo formulário padrão. Você **não tem acesso ao sistema interno real**, mas recebeu um **export fictício** com o estado dos clientes — o mesmo tipo de consulta que o suporte faz no dia a dia.

**Sua tarefa:** ler os tickets, cruzar com os dados, **priorizar a fila** e documentar a **trilha de troubleshooting** de cada caso — o passo a passo do que você faria (consulta → hipótese → retry → correção → escalação → resposta no Jira).

Não precisa instalar nada. Só ler os arquivos deste repositório.

---

## Regras

| Item | Detalhe |
|------|---------|
| Tempo estimado | até **2 horas** |
| Prazo | **2 dias corridos** a partir do convite |
| IA | permitida — declare uso em [docs/REVERSE.md](./docs/REVERSE.md) |
| Repo | dedicado — não compartilhe com outros candidatos |

---

## Por onde começar

1. [Glossário](./docs/GLOSSARIO.md)
2. [Fluxo IT-Support](./docs/FLUXO-IT-SUPPORT.md)
3. [Fila do dia](./FILA.md) — links para os 12 tickets
4. [Export do sistema interno](./data/sistema-interno-export.csv)
5. [Modelo de trilha](./docs/MODELO-TRILHA.md)
6. [Formato da entrega](./ENTREGA.md)

---

## Estrutura do repositório

| Caminho | Conteúdo |
|---------|----------|
| [INDICE.md](./INDICE.md) | Hub com links para tudo |
| [FILA.md](./FILA.md) | Visão da manhã |
| [tickets/](./tickets/) | IT-1001 … IT-1012 |
| [data/sistema-interno-export.csv](./data/sistema-interno-export.csv) | Dados para cruzar |
| [docs/](./docs/) | Glossário, fluxo, modelo |

---

## Entrega (resumo)

Crie na raiz do repositório:

1. `TRIAGEM.md` — ordem dos **12** tickets
2. `TRILHAS.md` — passo a passo do troubleshooting em **cada** ticket
3. `docs/REVERSE.md` — pergunta reversa + declaração de IA

Detalhes em **[ENTREGA.md](./ENTREGA.md)**.

---

## Dúvidas

Use o canal indicado no e-mail de convite.
