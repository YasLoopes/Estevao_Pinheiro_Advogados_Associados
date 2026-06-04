# Estevão & Pinheiro Advogados Associados

Repositório do projeto de automação financeira e operacional do escritório Estevão & Pinheiro Advogados Associados — Recife, PE.

---

## Sobre o projeto

Desenvolvimento de sistema financeiro sob medida para gestão de honorários, mensalidades de sindicatos, depósitos judiciais e relatórios para a direção. Construído em Python com integração ao ADV Box, bancos BB e Caixa Econômica Federal, e Google Workspace.

**Status atual:** Fase de diagnóstico e validação — aguardando informações de regras de negócio para iniciar o desenvolvimento.

---

## Documentos publicados

| Documento | Link |
|---|---|
| Jornada do sistema | [ver página](https://yasloopes.github.io/Estevao_Pinheiro_Advogados_Associados/jornada-sistema.html) |
| Overview de arquitetura | [ver arquivo](https://github.com/YasLoopes/Estevao_Pinheiro_Advogados_Associados/blob/main/diagn%C3%B3stico/overview-arquitetura.md) |
| Proposta comercial | [ver página](https://yasloopes.github.io/Estevao_Pinheiro_Advogados_Associados/proposta_ep.html) |
| Termo de aceite | [ver página](https://yasloopes.github.io/Estevao_Pinheiro_Advogados_Associados/termo_aceite.html) |
| Demo do sistema | [ver página](https://yasloopes.github.io/Estevao_Pinheiro_Advogados_Associados/demo_v2.html) |

---

## Estrutura do repositório

```
_memoria/          contexto do cliente, estratégia e aprendizados
diagnóstico/       arquivos das reuniões e overview de arquitetura
identidade/        design guide e assets visuais do escritório
dados/             bases de dados (sindicatos, beneficiários, processos)
scripts/           automações e utilitários
saidas/            documentos e relatórios gerados
```

---

## Stack planejada

- **Backend:** Python + FastAPI
- **Banco de dados:** Supabase (PostgreSQL)
- **Deploy:** Google Cloud Run
- **Auth:** Google OAuth 2.0 (Workspace do escritório)
- **IA:** Claude API (Anthropic)
- **Integração jurídica:** ADV Box API

---

**Desenvolvido por** Yasmin G. Lopes
