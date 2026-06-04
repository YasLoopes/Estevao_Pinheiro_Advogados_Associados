# Estevão & Pinheiro Advogados Associados

Esse é o sistema operacional do projeto Estevão & Pinheiro. Aqui ficam as regras de como operar nessa pasta — como carregar contexto, aprender com correções, manter tudo atualizado e criar skills novas conforme o projeto evolui.

---

## Contexto do projeto

No início de toda conversa, ler os seguintes arquivos (quando existirem e estiverem preenchidos):

1. `_memoria/empresa.md` — quem é o cliente, o que faz, como funciona o escritório
2. `_memoria/preferencias.md` — tom de voz, estilo de comunicação, o que evitar
3. `_memoria/estrategia.md` — foco atual, prioridades, prazos do projeto

Usar essas informações como base pra qualquer resposta ou entrega. Ao sugerir abordagens, formatos ou prioridades, considerar o foco atual descrito em `estrategia.md`.

Pra qualquer entrega visual (painel, proposta, apresentação, relatório), consultar `identidade/design-guide.md` como referência de estilo — paleta bordeaux/dourado, tom institucional sóbrio.

Não é necessário listar o que foi lido nem confirmar a leitura. Apenas usar o contexto naturalmente.

---

## Perfil do cliente: Empresa estabelecida

- Escritório com mais de 40 anos de atuação — peso institucional, decisões não são impulsivas
- Interlocutores são advogados e gestores — comunicação direta, sem rodeios, sem jargão de startup
- O argumento que fecha é concreto: tempo economizado, erro eliminado, visibilidade que antes não existia
- Potencial de longo prazo: cada entrega deve abrir espaço para a próxima automação

---

## Fluxo de trabalho

Antes de executar qualquer tarefa, verificar se existe skill relevante em `.claude/skills/`. Se encontrar, seguir as instruções da skill. Se não encontrar, executar a tarefa normalmente.

Ao concluir uma tarefa que não tinha skill mas parece repetível, perguntar:

> "Isso pode virar uma skill pra próxima vez. Quer que eu crie?"

Só quando o padrão de repetição for claro — não perguntar para tarefas pontuais.

---

## Aprender com correções

Quando o usuário corrigir algo, melhorar uma resposta ou dar instrução que parece permanente (frases como "na verdade é assim", "não faça mais isso", "prefiro assim", "sempre que...", "evita..."), perguntar:

> "Quer que eu salve isso pra não precisar repetir?"

Se sim, identificar onde faz mais sentido salvar:

- **Sobre o cliente** (serviços, processos, equipe, ferramentas) → `_memoria/empresa.md`
- **Sobre estilo e comunicação** (tom, formato, o que evitar) → `_memoria/preferencias.md`
- **Sobre prioridades e foco** (projetos, metas, prazos) → `_memoria/estrategia.md`
- **Sobre identidade visual** (cores, fontes, logo) → `identidade/design-guide.md`
- **Regra de comportamento nessa pasta** → este `CLAUDE.md`

Salvar com uma linha nova clara, sem reformatar o arquivo inteiro. Confirmar mostrando a linha adicionada.

---

## Manter contexto atualizado

Ao terminar uma tarefa que mudou algo relevante (nova informação do cliente, skill criada, mudança de escopo, processo mapeado, ferramenta identificada), perguntar:

> "Isso mudou algo no contexto. Quer que eu atualize a memória?"

Mostrar o que vai mudar antes de salvar. Não reformatar o arquivo inteiro.

**Quando NÃO perguntar:**
- Tarefas pontuais sem impacto no contexto (rascunho de email avulso, ajuste de texto)
- Perguntas simples ou conversas sem ação
- Mudanças já salvas pelo bloco de correções acima

---

## Criação de skills

Quando precisar criar skill nova para este projeto:

1. Verificar se existe template em `templates/skills/`
2. Perguntar se é específica deste projeto ou universal:
   - Específica → `.claude/skills/nome-da-skill/SKILL.md`
   - Universal → `~/.claude/skills/nome-da-skill/SKILL.md`
3. Calibrar o conteúdo da skill com base em `_memoria/empresa.md` e `_memoria/preferencias.md`
4. Se precisar de arquivos de apoio, criar dentro da pasta da skill

---

## Skills prioritárias a criar após o diagnóstico

- **`/relatorio-financeiro`** — gerar relatório de causas com valores por fonte e status
- **`/proposta-automacao`** — estruturar proposta comercial para novos processos a automatizar
- **`/mapear-processo`** — documentar processo manual identificado no diagnóstico

---

## Contexto pendente (a completar após diagnóstico de 21/05/2026)

- Tom de voz e estilo de escrita → `_memoria/preferencias.md`
- O que evitar na comunicação → `_memoria/preferencias.md`
- Gargalo principal confirmado → `_memoria/estrategia.md`
- Plataforma de destino dos dados (qual sistema recebe a planilha hoje) → `_memoria/empresa.md`
- Fontes exatas consultadas manualmente → `_memoria/empresa.md`
- Logo do escritório → `identidade/logo.png`

---

## Regras operacionais deste projeto

Derivam de erros reais cometidos neste projeto. Complementam as regras globais em `~/.claude/CLAUDE.md`.

### Validação visual antes de implementar
Antes de implementar qualquer mudança visual sem referência concreta (tamanho, cor, posição), perguntar o resultado esperado. "Ampliar o logo" sem medida de referência é ambíguo. Questionar antes, não retrabalhar depois.

### Cópia de arquivo com controle
Nunca copiar arquivo A → B sem antes verificar se B tem mudanças pendentes não presentes em A. Ao copiar, avisar explicitamente o que foi sobrescrito e o que ficou de fora.

### Cache do GitHub Pages
Quando o usuário disser que a demo "não atualizou", verificar primeiro se os arquivos são idênticos no disco. Se forem, o problema é cache — dizer isso claramente e orientar hard-refresh. Não commitar como solução de cache.

### Execução de instruções claras
Quando a instrução for direta ("salva", "commita", "publica"), executar sem perguntar opções. Perguntar só quando a instrução for ambígua ou tiver risco de perda de dado.

### Verificação de CSS antes de escrever
Antes de escrever regra CSS para uma classe, verificar se ela existe no HTML. Se não existir, avisar antes de escrever.

### Texto de UI é decisão, não detalhe
Antes de implementar qualquer label, botão ou mensagem de interface, perguntar o texto exato se não estiver especificado. Texto não é detalhe de implementação — cada iteração é um commit e um roundtrip desnecessário.

### Workaround é último recurso
Antes de criar scripts, pipelines ou conversões, perguntar se existe forma direta de resolver. Complexidade só quando o caminho simples foi descartado com motivo explícito.

### Commits por intenção, não por ação
Mudanças relacionadas à mesma feature ou correção vão em um único commit. Ajuste de label, CSS e JS do mesmo componente não são três commits separados.

### Bug conhecido não some da agenda
Quando um bug for identificado mas não corrigido por instrução pontual ("usa como está"), registrar e retomar na próxima oportunidade relevante. Não silenciar.

### Perguntas estratégicas antes de features estruturais
Para qualquer tarefa que altere estrutura, fluxo ou componente novo — não para ajustes pontuais —, perguntar antes de executar:
1. Qual o objetivo desta mudança no contexto do produto?
2. Quem vai ver, quando e em que contexto?
3. O que acontece depois — vai para produção? quem mantém?
4. Existe algo mais importante que deveria ser feito antes disso?
