# Estratégia

> O que importa agora. Prioridades, metas, prazos.
> O Claude usa isso pra decidir o que sugerir primeiro e o que adiar.
> Atualize sempre que as prioridades mudarem.

## Fase

**Desenvolvimento — Fase 1 (MVP)**  
Contrato fechado. Duas reuniões realizadas. Demo validada. Mapeamento completo do fluxo financeiro concluído.

## Prioridade principal

Construir o MVP operacional (Fase 1) para que Waldir pare de usar planilhas nas operações do dia a dia.

Bloqueio atual: aguardando materiais do cliente para iniciar:
- Extrato do BB (abril/maio) em OFX/CSV — Waldir
- Base de processos do ADV Box (CSV/Excel) — Andreele/Tiago
- Base de beneficiários FUNDEF (nome + CPF) — Andreele via Liz
- Lista de sindicatos com valor de mensalidade e % de repasse — Waldir
- API do ADV Box — Andreele

## Stack definida

- Frontend: Next.js / React
- Backend + banco: Supabase (PostgreSQL + Edge Functions + Auth Google)
- Extrato automático: Pluggy Open Finance (BB + Caixa)
- Assinatura digital: ZapSign (~R$150/mês)
- Deploy: Vercel (ou Google Cloud Run)
- Bot portal BB: Playwright/httpx (portal público sem login)

## Fases do projeto

**Fase 1 (6–8 semanas):** Auth Google, upload extrato, engine de padrões, painel financeiro, relatório WhatsApp, alerta inadimplência sindicatos, cálculo honorários  
**Fase 2 (4–6 semanas):** Pluggy Open Finance, ADV Box API, bot portal BB, fallback e-mail Caixa  
**Fase 3 (3–4 semanas):** ZapSign, automação ANF, cobrança automática

## Documentação do projeto

- `diagnóstico/contexto-waldir-diagnostico.md` — 1ª reunião (21/05)
- `diagnóstico/reuniao-02_29-05-26_contexto.md` — 2ª reunião (29/05)
- `diagnóstico/overview-arquitetura.md` — análise crítica + fluxograma + fases (aguardando validação)
