# Entrega — Teste Analista Sênior de Suporte

**Navegação:** [Índice](./INDICE.md) · [Fila](./FILA.md) · [Modelo de trilha](./docs/MODELO-TRILHA.md) · [Export](./data/sistema-interno-export.csv)

> Adicione os arquivos na **raiz do repositório** que você recebeu.

O foco da avaliação é a **trilha de troubleshooting** — o passo a passo do que você faria, não só a resposta pronta.

Leia o exemplo em [docs/MODELO-TRILHA.md](./docs/MODELO-TRILHA.md) antes de começar.

---

## 1. `TRIAGEM.md`

Liste os **12 tickets** na ordem de atendimento (use os IDs [IT-1001](./tickets/IT-1001.md) … [IT-1012](./tickets/IT-1012.md)).

Para cada um, **2–4 linhas**: impacto, resolve no suporte / orienta CX / escala.

Se agrupou tickets do mesmo cliente (ex.: [IT-1008](./tickets/IT-1008.md) e [IT-1011](./tickets/IT-1011.md)), explique aqui.

---

## 2. `TRILHAS.md` (principal)

Para **cada** ticket, use o bloco abaixo.

```
## IT-10xx — Título

### Trilha de troubleshooting

1. **Onde consulto primeiro:**
2. **O que busco:**
3. **O que encontrei:** (evidência — campos do CSV neste teste)
4. **Hipótese:**
5. **Retry / reprocesso:** (ação ou N/A + por quê)
6. **Correção manual no sistema interno:** (ação ou N/A + por quê)
7. **Escalação:** (para quem / N/A + por quê)
8. **Comunicação no Jira:** (texto curto, @agente.cx0x)
9. **Status final:** SOLUCIONADO | EM ANÁLISE | ESCALADO
```

**Importante:**

- Descreva o fluxo **como no dia a dia** (sistema interno, consulta read-only em banco de dados, reprocesso, fornecedor).
- Neste teste, a “consulta” é o [data/sistema-interno-export.csv](./data/sistema-interno-export.csv) — cite colunas/valores.
- Se um passo não se aplica, escreva **N/A** e justifique.

---

## 3. `ESCALACOES.md` (resumo opcional)

Tabela ou lista só dos tickets que você **escalaria**, com:

- Ticket · time destino · título sugerido · 2 linhas de contexto

Se preferir, pode omitir este arquivo e deixar tudo no passo 7 de cada trilha.

---

## 4. [docs/REVERSE.md](./docs/REVERSE.md)

Arquivo já existe como **template** — leia a explicação no topo do arquivo antes de preencher.

1. **Pergunta reversa** — um ponto do teste onde faltou contexto; a pergunta que você faria ao time e como isso mudaria sua trilha.
2. **Declaração de IA** — transparência sobre uso de ferramentas (permitido).

---

## 5. Checklist

- [ ] `TRIAGEM.md` — 12 tickets ordenados
- [ ] `TRILHAS.md` — 12 trilhas completas (passos 1–9)
- [ ] `docs/REVERSE.md`
- [ ] Link do repositório enviado
