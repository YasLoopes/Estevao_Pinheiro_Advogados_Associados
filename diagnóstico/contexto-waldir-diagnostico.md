# Diagnóstico — Waldir Rodrigues · Estevão & Pinheiro Advogados Associados
**Data da reunião:** 21/05/2026 · Conduzida por Yasmin

> Nota: Este arquivo consolida os insights extraídos das transcrições dos PDFs (waldir-advogados-associados-c_bM9aKfaF, waldir-advogados-associados-c_KRskyatp, Waldir - Advogados Associados.pdf). Os arquivos originais foram enviados na conversa — re-enviar para recuperar transcrição completa.

---

## Perfil do interlocutor
- **Waldir Rodrigues** — administrador financeiro do escritório
- Reporta a **Ricardo Estevam** — sócio proprietário/majoritário (não apenas gestor)

## Dor principal confirmada
- **"Me livrar das planilhas"** — frase literal de Waldir
- Processo atual: 3 planilhas distintas para controlar:
  1. RPVs e precatórios (causas ganhas)
  2. Despesas mensais do escritório
  3. Mensalidades de sindicatos (a receber)
- Consulta manual nos portais de cada banco (CEF, BB) para verificar disponibilidade
- Extração manual → planilha → processamento manual

## Cobrança de sindicatos — dor confirmada 100%
- **Sinijude deve 4-5 meses** de mensalidade ao escritório
- Cobrança atual: WhatsApp e telefone — 100% manual
- Feature de cobrança automática validada diretamente da transcrição

## Relatório para Ricardo
- **Entrega primária:** relatório 2x/semana via WhatsApp para Ricardo Estevam
- Ricardo quer visibilidade consolidada sem precisar entrar no sistema

## Reconhecimento de padrões — validado pelo próprio Waldir
- Waldir descreveu exatamente a feature de pattern learning:
  - Primeira vez que um lançamento aparece: pede para categorizar
  - Da segunda vez em diante: sugere categoria automaticamente
- Base para a abordagem OFX + engine de padrões

## Dados dos portais — NÃO são públicos
- Waldir confirmou: "Isso não são dados públicos, está fechado dentro de uma conta"
- Monitoramento automático de portais removido do escopo: correto
- Verificação continua manual (dor real, mas sem solução de API pública gratuita viável agora)

## Infraestrutura atual
- **Google Workspace** já em uso (Gmail, Drive)
- **Alter Data** = sistema de gestão do escritório (usado pela contabilidade Santos & Grit)
- Nova assistente administrativa sendo contratada

## Stack validada
- OFX / CSV / PDF para importação de extrato
- Supabase (backend PostgreSQL)
- Vercel (hosting)
- Assinatura digital: plataforma genérica (sem nome na demo)

## O que NÃO construir
- Monitoramento automático de portais → fora do escopo (custo/complexidade, não mencionado como prioridade)
- Portal alert hardcoded na demo → remover
