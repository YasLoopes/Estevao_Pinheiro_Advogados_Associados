# Aprendizados — Sessão de Diagnóstico

Registrado em 29/05/2026. Derivado do raio-x completo feito no chat de construção da demo_v2/v3.

---

## Padrões de uso identificados (lado Yasmin)

### Aprovação sem teste funcional
Mudanças eram aprovadas visualmente (pelo print/screenshot) antes de abrir o link real. Resultado: reversões frequentes após teste tardio. O padrão se repetiu pelo menos 3 vezes na sessão.

### "Não altere absolutamente nada" como muleta
A instrução apareceu em quase todas as tarefas. Indica falta de confiança de que o escopo seria respeitado — provavelmente porque em algum momento não foi. A instrução certa não é "não toque em nada", é "me diz antes o que vai mudar".

### Fluxo v2/v3 ineficiente por design
Validar na v3, copiar para v2 funciona, mas cria risco de divergência e não é como git deve funcionar. O modelo certo é branch: trabalha, valida, faz merge. Nunca foi discutido.

### Mobile aprovado sem teste em dispositivo real
A responsividade foi pedida, feita, revertida, e pedida de novo — sem briefing do que estava quebrado. "Está caótico" não é spec. Resultado: trabalho refeito às cegas.

### Iterações de copywriting em roundtrip
Labels de interface (accordion) mudaram 4 vezes em commits separados. Causa: texto não foi alinhado antes de implementar. Cada mudança foi um roundtrip desnecessário.

---

## Padrões de erro identificados (lado Claude)

### Complexidade antes do caminho simples
Criou script Python de 60 linhas + arquivo temporário para resolver um problema que uma pergunta teria evitado. Nunca perguntou: "você consegue me mandar o conteúdo como texto?"

### Bug de cache tratado como bug de código
Rodou 4 verificações técnicas para um problema que era Ctrl+Shift+R. Devia ter dito isso no primeiro turno.

### CSS para classes inexistentes
Escreveu regras CSS para `.nota-layout`, `.nota-section`, `.rh-kpi-row`, `.accum-row`, `.cau-table` e outras que não existiam no HTML. Inerte. Não verificou antes de escrever.

### Accordion tecnicamente frágil
A função `toggleAcc` reconstrói o `innerHTML` do botão inteiro a cada clique — destrói e recria o `<span>` com ID. Funciona mas é gambiarra. Aprovado sem questionar.

### Commits inflados por iteração
Cada ajuste de 3 linhas virou commit separado. Histórico polui o `git log` e reflete falta de planejamento antes de executar.

### Zero perguntas estratégicas
Em toda a sessão: nenhuma pergunta sobre o que acontece depois da demo. Quem a apresenta, para quem, quando, em qual contexto. Se vira produto, quem mantém. Quanto é descartável.

### Bug do `goCobranca` silenciado
Identificado no resumo de sessão anterior (função com `sindic.replace(/[^a-z]/g,'')` e ID errado). Tratado como instrução permanente ("usa como está"). Nunca retomado. Está no código em produção.

### Nunca removeu `<!-- saved from url -->`
Identificado como problema, documentado, e ignorado durante dezenas de edições subsequentes.

### Responsividade: classes inexistentes no HTML
Na primeira tentativa de CSS mobile, 8+ seletores escritos para classes que não existem no HTML. Verificação feita só depois, na segunda tentativa.

---

## Regras derivadas (já aplicadas no CLAUDE.md)

Ver seção `## Regras operacionais deste projeto` no CLAUDE.md raiz.
As 10 regras foram formalizadas e entram em vigor a partir da próxima sessão.

---

## Stack, backend Python e conexão com frontend HTML — 02/06/2026

**Gap:** Não sabia o que era "stack" nem como um backend Python se conecta ao HTML já existente (demo_v2.html).

**Regra aprendida:** Stack = ficha técnica das ferramentas do sistema. O frontend HTML não muda muito — o backend é a camada nova. Os dados hardcoded no HTML são substituídos por chamadas `fetch()` ao servidor Python. O visual pronto da demo é reaproveitado quase 100%.

**Exemplo concreto:** `var lancamentos = [{ data: "01/05" }]` → `const lancamentos = await fetch('/api/lancamentos').then(r => r.json())`. O servidor Python (FastAPI) recebe, busca no Supabase, devolve em JSON.

---

## Fuzzy matching para nomes de PIX — 02/06/2026

**Gap:** Não sabia o que era fuzzy matching nem por que é necessário neste projeto.

**Regra aprendida:** Fuzzy matching mede a similaridade entre dois textos (0–100%). Necessário porque o banco trunca nomes no extrato. "MARIA J SILVA" e "MARIA JOSE SILVA DOS SANTOS" são a mesma pessoa — `rapidfuzz` (Python) retorna 82% e o sistema pede confirmação ao Waldir.

**Regras práticas:** score >90 = aceitar automático / 70–90 = confirmar com Waldir / <70 = manual. Claude turbina com contexto (data, município, valor do lote).

---

## ADV Box API e Pluggy — 02/06/2026

**Gap:** Não sabia o que a API do ADV Box oferece, nem que Pluggy R$2.500/mês é o plano BÁSICO.

**ADV Box:** REST API com Bearer Token. Endpoints: processos, clientes, intimações, movimentações. Swagger em `https://api.softwareadvbox.com.br/docs`. Custo extra R$280/mês — verificar se incluso no plano atual. Risco: pode não ter endpoint para listar beneficiários de processos coletivos.

**Pluggy:** R$2.500/mês é o plano básico — sem opção mais barata. Para a Fase 1, upload manual de OFX/CSV (2 min/banco/semana) resolve sem custo adicional. Pluggy entra na Fase 2 somente se ROI estiver provado.

**Open Finance sem intermediário:** exige registro no BACEN como "Instituição Receptora de Dados" — processo regulatório para fintechs, inviável para escritório de advocacia.

---

## O que é um commit — 04/06/2026

**Gap:** Não sabia o que significa "comitar" no contexto do git.

**Regra aprendida:** Commit = foto do estado atual dos arquivos, com uma mensagem descrevendo o que mudou. O fluxo completo é sempre: `add` (seleciona o que entra na foto) → `commit` (tira a foto com mensagem) → `push` (envia para o GitHub). `/salvar` executa os três passos automaticamente.

**Exemplo concreto:** `jornada-sistema.html` e os arquivos de diagnóstico foram selecionados, registrados com a mensagem "Adiciona jornada do sistema, contexto da 2ª reunião e atualiza memória do projeto" e enviados para github.com/YasLoopes/Estevao_Pinheiro_Advogados_Associados.

---

## O que é um fork e por que não usar para projeto de cliente — 04/06/2026

**Gap:** Não sabia o que significa um fork no GitHub nem por que ele é problemático para um projeto dedicado a um cliente.

**Regra aprendida:** Fork = cópia conectada a um repositório de terceiro. Aparece publicamente como "forked from [conta]", herda o histórico de commits da origem e não é profissional para um projeto de cliente. A solução é criar um repositório novo do zero (sem fork), com histórico limpo iniciando no primeiro commit do projeto.

**Exemplo concreto:** o repositório `YasLoopes/MazyOS` era um fork de `mazzeoia/MazyOS`. Foi criado `YasLoopes/Estevao_Pinheiro_Advogados_Associados` do zero via branch órfã — sem conexão com nenhum terceiro, histórico iniciando em "Configuração inicial — Estevão & Pinheiro Advogados Associados".

---

## Bug pendente a corrigir

**`goCobranca(sindic)`** — função em demo_v2.html e demo_v3.html.
- Problema: `sindic.replace(/[^a-z]/g,'')` remove acentos e maiúsculas, não encontra o checkbox correto
- ID do checkbox referenciado: `chk-sindpd-inad` (hardcoded, não dinâmico)
- Status: identificado, não corrigido por instrução pontual. Retomar antes de apresentar a demo para o cliente.
