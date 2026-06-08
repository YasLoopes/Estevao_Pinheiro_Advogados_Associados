# Overview de Arquitetura — Sistema Financeiro E&P
**Preparado por:** Yasmin G Lopes  
**Data:** 02/06/2026   
**Apresentado a:** Waldir (Gerente Financeiro) e Andriely (Advogada Associada)

---

## Stack do projeto

| Camada | Ferramenta | Função |
|--------|-----------|--------|
| Frontend | HTML + CSS + JS (demo_v2.html) | Interface do usuário — já construída |
| Backend | Python + FastAPI | Servidor que processa pedidos e regras de negócio |
| Banco de dados | Supabase (PostgreSQL) | Armazena lançamentos, sindicatos, processos, beneficiários; exporta CSV/Excel a qualquer momento |
| IA / Inteligência | **Claude API (Anthropic)** | Categorização, fuzzy matching, relatórios, parsing de PDF, chat interno |
| Integração bancária Fase 1 | Upload OFX/CSV manual | Waldir exporta do banco, sobe no sistema — custo zero |
| Integração bancária Fase 2 | Pluggy Open Finance (opcional) | Automação total se justificado após Fase 1 |
| Autenticação | Google OAuth 2.0 | "Entrar com Google" — usa o Workspace já contratado |
| Deploy | **Google Cloud Run** | Hospedado dentro do Google — prioridade do cliente |
| Storage | Supabase Storage | PDFs de extratos, comprovantes, recibos |
| Segredos e credenciais | Google Secret Manager | Chaves de API nunca no código |
| E-mail | Gmail API (Google Workspace) | Relatórios, cobranças, solicitações para Tiago |
| Assinatura digital | ZapSign (~R$150/mês) | Recibo de honorários com assinatura por link |
| Gestão de processos | ADV Box API | Consulta de processos e beneficiários em tempo real |

---

## Decisão 1 — Por que build customizado

Não existe SaaS no mercado que cubra simultaneamente:
- Reconhecimento de transações por padrão (pattern learning por escritório)
- Cruzamento de beneficiários de processos coletivos com extrato bancário
- Regras de divisão de honorários configuráveis por sindicato/advogado (10%, 20%, 30%...)
- Integração com ADV Box para identificação de processos jurídicos específicos
**Conclusão:** build em Python, usando componentes prontos onde possível.

---

## Decisão 2 — Pluggy foi adiado para Fase 2

**R$2.500/mês é o plano básico do Pluggy** (confirmado em pluggy.ai/pricing). Não há plano mais barato.

Para a Fase 1 (MVP), o custo não se justifica antes de provar valor. Solução alternativa sem custo adicional:
- Waldir exporta extrato OFX/CSV do BB e Caixa (~2 minutos por banco, 1x/semana)
- Sistema processa automaticamente
- Para TEV (Caixa→Caixa): Gmail API envia e-mail automático para Tiago, processa resposta com Claude


**Pluggy entra na Fase 2 se:** após 2 meses com o sistema funcionando, o upload manual ainda for um ponto de atrito.

---

## Decisão 3 — Claude API é componente central

Claude (Anthropic) é necessário para:

| Uso | Impacto |
|-----|---------|
| Categorizar transações desconhecidas | Identifica sindicato/processo pelo contexto (valor, data, histórico) quando nenhum padrão algorítmico resolve |
| Processar comprovante enviado por Tiago | Tiago responde com foto do comprovante da Caixa — Claude lê a imagem e extrai processo, valor e data sem ação de Waldir |
| Gerar relatório semanal | Mesmo formato que Waldir já envia para Ricardo Estevam, automaticamente |
| Parsear PDFs irregulares | Extratos em PDF sem estrutura limpa — Claude extrai mesmo assim |
| Rascunhar e-mails de cobrança | Tom institucional do escritório, para sindicatos inadimplentes |
| Respostas em linguagem natural (Fase 2) | Waldir digita "o Sinijude pagou esse mês?" — recebe resposta direta |

---

## Decisão 4 — Onde fica o código

O código Python do backend vive em um **repositório GitHub privado**. O Google Cloud Run puxa desse repositório e implanta automaticamente a cada push.

```
Yasmin desenvolve localmente → push para GitHub → Cloud Run implanta automaticamente
```

Cada integração é um arquivo separado em `services/`: `extrato.py`, `claude_api.py`, `gmail.py`, `advbox.py`, `bot_bb.py`. Todas as chamadas de API (Claude, Gmail, WhatsApp, ADV Box) são feitas diretamente pelo Python — sem n8n, sem intermediário.

---

## Decisão 5 — Chat com Claude dentro do sistema (Plus)

Janela de chat integrada ao painel HTML. Waldir digita em linguagem natural; Claude responde com os dados reais do banco.

Exemplos de perguntas:
- "O Sinijude pagou esse mês?"
- "Qual o total de honorários do FUNDEF em maio?"
- "Gera o resumo do mês para mandar pro Ricardo"
- "Quais sindicatos estão com mais de 2 meses em atraso?"

O Python recebe a pergunta, busca os dados relevantes no Supabase, envia para Claude com contexto, devolve a resposta formatada.

---

## Decisão 6 — Google em todo o stack (prioridade do cliente)

- Deploy: **Google Cloud Run** (não Vercel)
- Auth: Google OAuth 2.0 via Supabase
- E-mail: Gmail API via Google Workspace já contratado
- Segredos: Google Secret Manager
- Dados: Supabase com datacenter — dentro do Google Cloud

---

## Fluxo de identificação de transações

```
Waldir sobe OFX/CSV (ou futuramente Pluggy traz automático)
         │
         ▼
  Já foi vista antes?  (pattern match por CNPJ/nome/valor)
         │
   SIM ──┤── NÃO
         │         │
         ▼         ▼
  Categorização  Claude analisa contexto → sugere
  sugerida       Waldir confirma → sistema aprende
         │
         ▼
  É mensalidade de sindicato?
  → Cruzar CNPJ/nome com cadastro de 15 sindicatos → automático
         │
  É PIX de pessoa física?
  → rapidfuzz → fuzzy match algorítmico com base ADV Box (FUNDEF, IPCEP, Bom Jardim)
  → score > 90: aceitar | 70–90: exibe sugestão para Waldir confirmar | < 70: manual
  (Claude NÃO é chamado aqui — o matching é puramente algorítmico; Gray zone vai para Waldir)
         │
  É depósito judicial (BB)?
  → Bot Python consulta portal público BB com protocolo → identifica processo → automático
         │
  É TEV/depósito judicial (Caixa)?
  → Fase 1: Gmail API envia e-mail automático para Tiago → Claude processa resposta
  → Fase 2: Pluggy identifica pagador diretamente
         │
  Não identificado → Waldir classifica → sistema aprende para próxima vez
```

---

## Decisão 7 — Fuzzy matching com rapidfuzz, sem IA

PIX de pessoa física traz nome truncado pelo banco (ex.: "JOSE M SILVA"). O matching contra a base de beneficiários (FUNDEF, IPCEP, Bom Jardim, Sirinhaém) é feito com **rapidfuzz** (biblioteca Python — sem chamada à API Claude).

| Score | Ação |
|-------|------|
| > 90% | Aceita automaticamente |
| 70–90% | Exibe sugestão; Waldir confirma com 1 clique |
| < 70% | Classificação manual; sistema aprende para próxima vez |

Claude **não é chamado** neste passo. Isso reduz o volume de requisições à API em ~40% e elimina latência desnecessária para uma operação que é puramente algorítmica.

> **Impacto no custo:** estimativa de Claude API cai de R$100–300/mês para R$60–180/mês na Fase 1 (dependendo do volume de transações desconhecidas e comprovantes do Tiago).

---

## Fluxograma de integração

```
┌────────────────────────────────────────────────────────────────────┐
│  FONTES DE DADOS                                                   │
│                                                                    │
│  Waldir exporta OFX/CSV ──────────────────┐                        │
│  (BB e Caixa — 2 min/semana)              │                        │
│                                           ▼                        │
│  Fase 2: Pluggy Open Finance ─────► Upload no sistema              │
└────────────────────────────────────────┬───────────────────────────┘
                                         │
                                         ▼
┌────────────────────────────────────────────────────────────────────┐
│  ENGINE DE IDENTIFICAÇÃO (Python + Claude API)                     │
│                                                                    │
│  Pattern match → Sindicato? → PIX pessoa física? →                 │
│  Depósito judicial BB (bot)? → TEV Caixa (e-mail Tiago)?           │
│  Não identificado: Waldir classifica → aprende                     │
└────────────────────┬──────────────────┬────────────────────────────┘
                     │                  │
          ┌──────────┘                  └──────────────┐
          ▼                                            ▼
┌──────────────────┐              ┌───────────────────────────────────┐
│  BOT PORTAL BB   │              │  ADV BOX API + BASE BENEFICIÁRIOS │
│  Protocolo+CNPJ  │              │  572 processos · Bearer Token     │
│  Portal público  │              │  FUNDEF · IPCEP · Bom Jardim      │
│  sem login       │              │  Swagger: api.softwareadvbox.com  │
│  Custo: zero     │              │  Custo extra: R$280/mês (verificar│
└──────────────────┘              │  se incluso no plano atual)       │
                                  └───────────────────────────────────┘
                                               │
                                               ▼
┌────────────────────────────────────────────────────────────────────┐
│  SUPABASE — banco central / Google Cloud                           │
│  Lançamentos · Sindicatos · Processos · Beneficiários              │
│  Regras de honorário · Histórico de padrões · Log de auditoria     │
└──────────────────────────┬─────────────────────────────────────────┘
                           │
       ┌───────────────────┼──────────────────────┐
       ▼                   ▼                      ▼
┌───────────────┐ ┌─────────────────────┐ ┌─────────────────────┐
│  PAINEL WEB   │ │  SAÍDAS AUTOMÁTICAS   │  RELATÓRIOS         │
│               │ │                     │ │                     │
│  Login Google │ │  Cálculo honorário  │ │  Semanal → Gmail    │
│  Dashboard    │ │  + tributos auto    │ │  para Ricardo       │
│  receita/custo│ │                     │ │  (Fase 3: WhatsApp) │
│  Inadimplência│ │  ZapSign: recibo    │ │                     │
│  sindicatos   │ │  assinatura digital │ │  Cobrança automática│
│               │ │                     │ │  sindicatos         │
│               │ │                     │ │  inadimplentes      │
└───────────────┘ └─────────────────────┘ └─────────────────────┘
```

---

## Fases de entrega

### Fase 1 — MVP operacional (6–8 semanas)
**Waldir para de usar planilhas no dia a dia.**

- [ ] Auth Google + frontend base (ajustar demo_v2.html com chamadas fetch reais)
- [ ] Upload de extrato OFX/CSV/PDF + parser automático
- [ ] Engine de padrões (pattern learning com Claude)
- [ ] Cadastro de 15 sindicatos com regras de repasse
- [ ] Painel financeiro: receita / custo / saldo / últimos 6 meses
- [ ] Cálculo automático de honorários + tributos (substitui planilha de RPV)
- [ ] Alerta de inadimplência de sindicatos (threshold configurável)
- [ ] Bot portal público BB (depósito judicial sem login)
- [ ] Gmail API: relatório semanal automático para Ricardo Estevam
- [ ] Gmail API: solicitação automática de comprovante para Tiago (Caixa)

**Entrega:** sistema em produção. Waldir sobe o extrato 1x/semana e o sistema faz o restante.

---

### Fase 2 — Automação bancária (4–6 semanas após Fase 1)
**Elimina o upload manual se justificado pelo uso.**

- [ ] Pluggy Open Finance (BB + Caixa automático) — avaliar ROI real antes de contratar
- [ ] Cruzamento ADV Box API em tempo real (beneficiários por processo)
- [ ] Fuzzy matching com contexto Claude para PIX pessoa física

---

### Fase 3 — Automação de saídas (3–4 semanas após Fase 2)
**Elimina trabalho manual de emissão de documentos.**

- [ ] Assinatura digital de recibos de honorários (ZapSign)
- [ ] Cobrança automática de mensalidades (Gmail → WhatsApp se necessário)
- [ ] WhatsApp Business API para Ricardo (se preferir ao Gmail)

---

### Fase 4 — Opcional (sem prazo, entra quando houver demanda)

- [ ] Pluggy Open Finance — se upload manual se tornar ponto de atrito após Fase 1
- [ ] Restrições de acesso por perfil — se necessário controlar o que cada usuário vê

---

## Segurança — o que, como e com qual ferramenta

Acesso ao sistema: quem tiver login (Google OAuth) vê o sistema completo. Restrições por perfil podem ser adicionadas futuramente se necessário.

| O que proteger | Ferramenta | Como funciona |
|---------------|-----------|---------------|
| Login e identidade | Google OAuth 2.0 | E-mail Workspace existente; Google responsável pela autenticação |
| Dados em trânsito | HTTPS/TLS (Cloud Run) | Criptografia automática em toda comunicação |
| Dados armazenados | Supabase AES-256 | Mesmo que alguém acesse o servidor físico, não lê os dados |
| Credenciais bancárias | Nunca armazenadas | Upload OFX: senha do banco fica só no app do banco |
| Chaves de API | Google Secret Manager | Claude API, ADV Box, Gmail — nunca no código, nunca no GitHub |
| Auditoria | Log automático Supabase | Toda ação registrada: quem, o quê, quando, IP |
| Backup | Supabase diário + Google Drive | Dados nunca perdidos |
| Dados no Brasil | Supabase br-southeast | LGPD: dados de beneficiários ficam em território nacional |
| Export de emergência | Supabase CSV + openpyxl Python | Waldir pode exportar Excel a qualquer momento — dados nunca presos |

**Mensagem para o escritório:**
> "Seus dados ficam dentro do Google. A senha do banco nunca toca o sistema. Login é o e-mail Google que vocês já têm. Todo acesso fica registrado."

---

## WhatsApp / Gmail para Ricardo

**Fase 1 — Gmail (custo zero, já está no Workspace):**
- Python gera relatório semanal no mesmo formato que Waldir já envia hoje
- Gmail API envia de `financeiro@estevaoepinheiro.adv.br` para Ricardo toda sexta às 8h
- Configuração: ~2 dias de desenvolvimento

**Fase 3 — WhatsApp (se Ricardo preferir):**
- Meta Business API: aprovação de templates (~1 semana)
- Evolution API (open source) hospedado no Cloud Run: ~R$50/mês de infraestrutura
- Custo de mensagem: ~R$0,08/msg

---

## Consentimento Open Finance (Fase 2)

Banco Central do Brasil: consentimento válido até 12 meses. Após isso, automação bancária para.

**O que o sistema faz:**
- 30 dias antes da expiração: e-mail automático para Waldir com link para renovar
- 7 dias antes: segundo alerta
- No dia: sistema entra em modo "upload manual" automaticamente (fallback)
- Renovação: Waldir clica no link → abre o app do banco → 3 cliques → renovado

---

## ADV Box API — limitações conhecidas

| Limitação | Probabilidade | Impacto | Ação |
|-----------|--------------|---------|------|
| Rate limits não documentados | Alta | Médio | Testar antes; implementar cache local |
| Sem webhooks (precisa polling) | Alta | Baixo | Polling a cada 15 min — aceitável |
| Endpoint de beneficiários pode não existir | Média | Alto | Verificar no Swagger antes de desenvolver |
| Custo extra R$280/mês | Alta | Baixo | Verificar se incluso no plano atual do escritório |

**Documentação Swagger:** `https://api.softwareadvbox.com.br/docs` — verificar com Andriely.

---

## Riscos e mitigações

| Risco | Prob | Impacto | Mitigação |
|-------|------|---------|-----------|
| ADV Box não tem endpoint de beneficiários por processo | Média | Alto | Fallback: CSV exportado mensalmente |
| Portal nfse.gov.br muda layout | Baixa-média | Baixo | Monitoramento + alerta + fallback manual |
| Open Finance: renovação de consentimento esquecida (Fase 2) | Alta | Médio | Alertas automáticos 30 e 7 dias antes |
| Nome PIX muito abreviado — fuzzy não resolve | Alta | Médio | Confirmação manual; sistema aprende com o tempo |
| Ricardo Estevam não usa e-mail regularmente | Média | Médio | WhatsApp como alternativa na Fase 3 |

---

## Custos recorrentes estimados

| Serviço | Custo mensal | Observação |
|---------|-------------|------------|
| Supabase (Pro) | ~$25/mês (~R$130) | Grátis no início; pago quando volume crescer |
| Google Cloud Run | ~R$50–100 | Pay-per-use; baixo no início |
| ZapSign (Fase 3) | ~R$150 | Assinatura digital de recibos |
| ADV Box API | ~R$280 | Verificar se incluso no plano atual |
| Claude API | ~R$100–300 | Depende do volume; cobrança por uso |
| **Total Fase 1** | **~R$530–780/mês** | Sem Pluggy, sem ZapSign |
| **Total Fase 3** | **~R$800–1.200/mês** | Com ZapSign, Claude, Cloud Run, ADV Box |

> Pluggy (R$2.500/mês) deliberadamente excluído da Fase 1. Avaliar na Fase 2 com ROI comprovado.

---
