# Empresa

> Memória central do negócio. O Claude lê esse arquivo antes de cada resposta.
> Preenchido pelo `/instalar` — você pode editar a qualquer momento.

**Nome:** Estevão & Pinheiro Advogados Associados

**Negócio:** Escritório de advocacia trabalhista e sindical — atua exclusivamente pelo lado do trabalhador há mais de 40 anos (fundado em 1983, Recife - PE).

**O que faz:** Representação jurídica em Direito do Trabalho, Previdenciário, Administrativo, Constitucional, Eleitoral, Consumerista e Cível. Atua em todas as instâncias da Justiça do Trabalho: dissídios individuais e coletivos, ações coletivas trabalhistas, consultorias preventivas, pareceres jurídicos, acordos e convenções coletivas, mediação junto ao MPT.

**Perfil:** Escritório estabelecido, institucional. Parceiro estratégico de sindicatos da classe trabalhadora. Posicionamento de tradição + inovação.

**Atende clientes:** Trabalhadores e sindicatos (ex: SINDSEP, SINDPD, SEEB, SINDIFISCO e outros). Não atua pelo lado do empregador.

**Equipe:** Equipe jurídica (advogados especializados em diversas áreas do direito trabalhista) + equipe administrativa.

**Ferramentas atuais:**
- **ADV Box** — gestão de processos jurídicos (572 ativos de ~3.000 totais); em uso há < 1 mês; tem API disponível; exporta CSV/Excel; categorias: herdeiros FUNDEF (grupo civil) e beneficiários licença-prêmio (grupo administrativo)
- **AlterData** — sistema de contabilidade (da Santos & Grit, contabilidade parceira); módulo financeiro foi liberado recentemente; Waldir vai alimentar após treinamento
- **Google Workspace** — e-mail, Drive, site do escritório; base da infraestrutura do projeto de automação
- **Banco do Brasil + Caixa Econômica Federal** — contas jurídicas; extratos em OFX/CSV/PDF; portal público BB de resgate de depósito judicial (sem login, protocolo + CNPJ); Caixa não tem plataforma pública equivalente
- Portais TRT (6ª e 15ª Região), SIAPE, Bradesco — consultados manualmente

**Principais entregas:** Processos trabalhistas individuais e coletivos, acordos e convenções coletivas, consultorias preventivas, pareceres, representação em mediações.

**Contato:** contato@estevaoepinheiro.adv.br · (81) 3423-2494 · Rua Dom Vital, 48 - Santo Amaro, Recife - PE

**Pessoas-chave do projeto:**
- **Waldir** — administrador financeiro; interlocutor principal no dia a dia; reporta a Ricardo Estevam
- **Andreele** — jurídico; responsável pelas bases de beneficiários (FUNDEF, IPCEP, Bom Jardim, Sirinhaém)
- **Ricardo Estevam** — sócio proprietário; recebe relatório semanal via WhatsApp; baixa familiaridade com tecnologia
- **André** — participou da 2ª reunião; envolvido na parte técnica/bancária
- **Tiago** — tesoureiro da Caixa Econômica; fornece comprovantes de TEV/depósito judicial manualmente por e-mail

**Bases de beneficiários existentes:**
- FUNDEF — planilha Google Spreadsheets (nome + CPF + processo); separada do ADV Box
- IPCEP — a ser fornecida
- Bom Jardim — ~100 pessoas; sem processo judicial, só administrativo; via ADV Box
- Sirinhaém — a ser fornecida

**Fluxo financeiro (resumo):**
- Entradas: mensalidades de ~15 sindicatos (PIX/TED) + honorários via depósito judicial (BB e Caixa) + PIX de pessoas físicas (beneficiários FUNDEF/IPCEP)
- Saídas: repasse de honorários para advogados (% variável por sindicato) + fundo fixo em cheque (R$1.000–R$1.600/mês) + nota fiscal ANF mensal para SINTEP
- 3% de IR retido pela Caixa automaticamente em depósitos judiciais

## Contexto adicional

Projeto de automação contratado. Duas reuniões realizadas (21/05 e 29/05/2026). Demo do sistema financeiro validada. Desenvolvimento iniciando — ver `diagnóstico/overview-arquitetura.md` para arquitetura completa.
