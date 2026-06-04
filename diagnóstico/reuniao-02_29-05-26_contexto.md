# Contexto — 2ª Reunião — 29/05/2026

**Cliente:** Estevão e Pinheiro Advogados  
**Participantes:** Yasmin (dev), Valdir (financeiro), Andreele (jurídico), André  
**Objetivo:** Validação da demo do sistema financeiro e levantamento de requisitos operacionais

---

## 1. Decisões tomadas

- O sistema será acessado via **login Google** (Google Workspace já contratado)
- **Armazenamento centralizado no Google Cloud / Google Workspace** — não migrar para outra nuvem
- Recibo de honorários com **assinatura digital** via ZapSign ou ClickSign (avaliar custo — ZapSign ~R$150/mês)
- Se existir plataforma SaaS que já atenda, contratar e Yasmin faz a implementação/integração — não necessariamente construir do zero
- Será criado um **grupo de comunicação único** entre Yasmin, Valdir e Andreele para conduzir o projeto

---

## 2. Sistemas existentes mapeados

| Sistema | Uso atual | Status | Integração planejada |
|---------|-----------|--------|----------------------|
| **ADV Box** | Gestão de processos jurídicos (572 ativos de ~3.000 totais) | Em uso, < 1 mês | API disponível — cruzar beneficiários com extratos |
| **AlterData** | Contabilidade | Ativo; financeiro foi liberado recentemente | Treinamento pendente; Waldir vai alimentar |
| **Google Workspace** | E-mail, site, armazenamento | Ativo | Base da infraestrutura do novo sistema |
| **Banco do Brasil** | Conta jurídica | Ativo | Plataforma pública de resgate de depósito judicial (sem login, só protocolo + CNPJ) |
| **Caixa Econômica Federal** | Conta jurídica | Ativo | **Sem plataforma pública confirmada** — ver nota pós-reunião |

---

## 3. Fluxo financeiro real (como funciona hoje)

### 3.1 Entradas por tipo

**Mensalidades de sindicatos**
- ~15 sindicatos pagam mensalidade via PIX ou TED
- Waldir checa BB + Caixa manualmente 2–3 vezes por semana para confirmar pagamentos
- Cada sindicato tem percentual de repasse diferente (10%, 20%, 30%...) — tudo registrado

**Honorários via depósito judicial — Banco do Brasil**
- Processo: extrato BB → identificar número de protocolo → acessar plataforma pública BB → puxar comprovante → identificar processo + sindicato
- Plataforma BB: pública, sem login, entrada por protocolo + CNPJ — **pode ser automatizada com bot/macro**

**Honorários via depósito judicial — Caixa Econômica**
- Processo: Waldir contata Tiago (tesoureiro da Caixa) por e-mail solicitando comprovantes → Tiago imprime fisicamente e manda PDF por foto
- Extrato da Caixa não traz detalhamento suficiente para identificar o processo
- **Gargalo crítico — ver nota pós-reunião**

**TEV (Banco do Brasil para Caixa)**
- Mais difícil de rastrear que TED
- Waldir solicita por e-mail ao Tiago detalhamento com valor e data → Tiago responde com comprovante

**PIX de pessoas físicas (honorários individuais)**
- Aparece no extrato com nome da pessoa física
- Problema: nome pode estar abreviado diferente da base de dados
- Necessidade: cruzar nome do PIX com base de beneficiários (FUNDEF, IPCEP, Bom Jardim, Sirinhaém...)

### 3.2 Processos coletivos — regra de negócio crítica

- Um processo pode ter **centenas ou milhares de beneficiários** (ex: processo 356 com ~1.500 pessoas, FUNDEF)
- Pagamento é **faseado em lotes** — não entra tudo de uma vez (ex: 300 pessoas no lote 1, 200 no lote 2...)
- Número de parcelas pode ser negociado com o tribunal — Waldir e Andreele não sabem com antecedência
- Valor atualiza a cada lote pago
- **Chave de identificação: número do processo** (único campo estável entre todos os lotes)
- Processos têm numeração dupla em alguns casos: número originário + número eletrônico

### 3.3 Saídas

**Fundo fixo (cheque)**
- Saque mensal de R$1.000–R$1.600 para despesas miúdas (gasolina, etc.)
- Identificado no extrato como "fundo fixo"
- Prestação de contas feita manualmente

**Repasse de honorários para advogados**
- Calculado sobre valor líquido (após retenção de 3% IR pela Caixa)
- Divisão varia por entrada — % diferente por sindicato/processo
- Transferência bancária → banco gera PDF do comprovante
- Waldir quer: jogar esse PDF no sistema → sistema gera recibo automaticamente

**Nota fiscal (ANF)**
- Emitida mensalmente para cada SINTEP/cliente
- Processo atual: portal federal com CNPJ + senha + token físico, preencher manualmente, gerar PDF, baixar, modelo Word, atualizar data/valor, gerar PDF do recibo, enviar por e-mail
- **Pode ser automatizado com agente rodando em servidor local** (tem token físico da PJ)

### 3.4 Relatório para a direção

- Waldir envia relatório para Ricardo Estevam via WhatsApp
- Prazo: até dia 10 do mês seguinte para fechar o mês anterior
- Formato atual: manual, valores de entradas discriminadas por origem

---

## 4. Funcionalidades validadas na demo

| Funcionalidade | Validada | Observação |
|----------------|----------|------------|
| Upload de extrato (OFX/CSV/PDF) e leitura automática | ✅ | BB e Caixa geram esses formatos |
| Reconhecimento automático de transações já mapeadas | ✅ | Mostra como "reconhecido", usuário confirma |
| Classificação manual para transações novas | ✅ | Custo fixo / custo variável / honorário |
| Cálculo automático de honorário + tributos por lançamento | ✅ | Substitui planilha de RPV |
| Painel financeiro com receita/custo dos últimos 6 meses | ✅ | Atualizado em tempo real com extratos |
| Relatório semanal pré-montado para copiar no WhatsApp | ✅ | Pode integrar IA para envio direto |
| Alerta de sindicatos sem pagamento há N meses | ✅ | Configurar threshold |
| Recibo de honorários com assinatura digital | ✅ parcial | Integrar ZapSign/ClickSign — avaliar custo |
| Acompanhamento de assinaturas pendentes | ✅ | Via webhook das plataformas de assinatura |
| Painel de inadimplência com vencimentos | ✅ | Baseado em valor de mensalidade cadastrado |
| Distribuição de receita por banco (BB vs Caixa) | ✅ | — |
| Total a receber por processo (causa monitorada) | ❌ | Não existe esse controle atualmente — remover |
| Alertas de RPV vencendo em 60 dias | ❌ | Responsabilidade jurídica, não financeira — remover |

---

## 5. Bases de dados a preparar antes do desenvolvimento

| Base | Fonte | Conteúdo | Formato disponível |
|------|-------|----------|--------------------|
| Cadastro de sindicatos | Waldir | Nome, CNPJ, valor mensalidade, % repasse | Planilha manual |
| Beneficiários FUNDEF | Google Spreadsheets (fora do ADV Box) | Nome + CPF + processo vinculado | CSV/Google Sheets |
| Beneficiários IPCEP | — | Nome + processo | — |
| Beneficiários Bom Jardim | ADV Box fornecida por Andreele | ~100 pessoas, sem processo judicial, só administrativo | ADV Box |
| Beneficiários Sirinhaém | A ser fornecido | Idem Bom Jardim | ADV Box |
| Processos ADV Box | ADV Box (572 cadastrados) | Tipo de ação, nome, número processo | CSV/Excel disponível; API disponível |
| Advogados e % honorário | Waldir | Nome, % por tipo de processo | — |
| Regras de divisão por entrada | Waldir | % por sindicato (10/20/30...) e divisão entre sócios | Planilha |

---

## 6. Pendências e responsáveis

| # | Ação | Responsável | Status |
|---|------|-------------|--------|
| 1 | Investigar se Caixa tem plataforma pública de comprovante de depósito judicial | Waldir (perguntar a Tiago) | **Resolvido: NÃO existe** |
| 2 | Enviar extrato do BB (abril e maio) em OFX/CSV para teste | Waldir | Pendente |
| 3 | Exportar base de processos do ADV Box (CSV/Excel) | Andreele / Tiago | Pendente |
| 4 | Fornecer base de beneficiários FUNDEF com nome + CPF | Andreele (via Liz) | Pendente |
| 5 | Fornecer lista completa de sindicatos com valor mensalidade e % repasse | Waldir | Pendente |
| 6 | Avaliar plataformas SaaS existentes | Yasmin | Em andamento |
| 7 | Dar API do ADV Box para Yasmin | Andreele | Pendente |
| 8 | Criar grupo de comunicação (Yasmin + Valdir + Andreele) | Qualquer um | Pendente |
| 9 | Avaliar custo e usabilidade ZapSign vs ClickSign | Yasmin | Pendente |

---

## 7. Perguntas em aberto

- ~~Caixa Econômica tem portal público para consulta de comprovante de depósito judicial via CNPJ?~~ → **Não existe** (confirmado pós-reunião)
- Quantos sindicatos exatamente estão ativos? (Waldir menciona ~15)
- Como identificar, no extrato, se um crédito via TEV vem de um processo específico quando não há informação além do CNPJ? → **Pluggy via Open Finance consegue identificar o pagador** (confirmado pós-reunião)
- Os processos de Bom Jardim e Sirinhaém precisam entrar no ADV Box como "processos administrativos internos" ou ficam só na base avulsa?
- Como lidar com nomes abreviados diferentes entre extrato bancário e base de dados? (fuzzy matching?)
- Qual o fluxo exato quando um processo tem honorário retido pela Justiça diretamente na conta do advogado (ex: Cláudio) — como entra no sistema?

---

## 8. Restrições de negócio

- **Processos coletivos com múltiplos beneficiários e lotes:** a chave de vínculo é sempre o número do processo, nunca o valor
- **3% de IR** é retido pela Caixa automaticamente em depósitos judiciais — sistema deve sempre trabalhar com valor líquido pós-retenção
- **Fundo fixo em cheque** é a única despesa que não passa pelo extrato de forma detalhada — tratado como categoria única
- **Token físico de CNPJ** existe para emissão de nota fiscal — qualquer automação de ANF depende de acesso à máquina local
- **Ricardo Estevam e João** têm baixa familiaridade com tecnologia — qualquer fluxo que passe por eles precisa ser o mais simples possível
- **Assinaturas digitais** já são aceitas pelos advogados mais jovens (Andriel, Breno, André) — não pelos sócios sêniors
- O sistema deve funcionar dentro do **Google Workspace já contratado** — minimizar novos custos de infraestrutura

---

## Notas pós-reunião (contexto adicional)

1. **Caixa Econômica confirmou que NÃO há plataforma pública** para resgate de comprovantes de RPV e Precatórios
2. **Para TEV (Caixa a Caixa), o Pluggy via Open Finance consegue identificar o pagador**
