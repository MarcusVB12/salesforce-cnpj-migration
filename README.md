# salesforce-cnpj-migration

> Agents e prompts do GitHub Copilot para migrar qualquer projeto Salesforce para suportar o **novo formato alfanumérico do CNPJ** (IN RFB nº 2.229/2024, vigente a partir de julho de 2026).

---

## O que este toolkit faz

O novo formato de CNPJ emite identificadores alfanuméricos de 14 caracteres (`[A-Z0-9]{12}[0-9]{2}`). Projetos Salesforce existentes que validam ou processam CNPJs precisam ser atualizados para aceitar o novo formato — caso contrário, **rejeitarão registros válidos a partir de julho de 2026**.

Este toolkit oferece:
- **Descoberta automática** de todos os arquivos impactados no seu projeto
- **Migração passo a passo** com prévia do diff e confirmação antes de cada alteração
- **Referência técnica** para o algoritmo correto (Módulo 11 com valores ASCII-48)

---

## Arquivos incluídos

```
.github/
├── agents/
│   ├── cnpj-alphanumerico.agent.md     ← Referência técnica do CNPJ (universal — não altere)
│   └── salesforce-apex.agent.md        ← Convenções de código Apex (adapte ao seu projeto)
└── prompts/
    ├── salesforce-project.prompt.md    ← Padrões de busca + mapa de impacto (seções 1-4 estáticas, seção 5 gerada em tempo de execução)
    └── cnpj-migration-generic.prompt.md ← Orquestrador de migração (universal — não altere)
```

---

## Instalação

**Copie a pasta `.github/` para a raiz do seu projeto Salesforce.**

```
seu-projeto-salesforce/
└── .github/          ← copie esta pasta inteira aqui
    ├── agents/
    └── prompts/
```

Requisitos:
- VS Code com GitHub Copilot (modo agente)
- Versão do Copilot que suporte arquivos de customização `.agent.md` e `.prompt.md`

---

## Adaptando ao seu projeto (opcional, mas recomendado)

Edite `.github/agents/salesforce-apex.agent.md` e substitua as seções de placeholder pelas informações reais do seu projeto:
- Descrição da arquitetura
- Tabela de convenções de nomenclatura
- Regras PMD / qualidade

Os outros 3 arquivos funcionam sem alteração para qualquer projeto Salesforce.

---

## Como usar

1. Abra o VS Code no seu projeto Salesforce
2. Abra o painel do Copilot Chat em **modo Agente**
3. Digite `/cnpj-migration-generic`

O agente irá:

```
FASE 0  → lê os padrões de busca de salesforce-project.prompt.md (seções 1-4)
        → varre todo o projeto em busca de arquivos relacionados a CNPJ
        → escreve o mapa de impacto na seção 5 de salesforce-project.prompt.md
        → exibe o mapa → aguarda sua confirmação

FASE 1  → apresenta o plano de execução ordenado (CRÍTICO → ALTO → MÉDIO)
        → aguarda sua confirmação para iniciar

FASE 2  → para cada arquivo:
            lê o conteúdo real do arquivo
            analisa o que precisa mudar
            exibe um diff (ANTES / DEPOIS)
            aguarda seu [sim / não / pular]
            aplica somente após confirmação

FASE 3  → checklist final com itens que exigem revisão manual
          (Regras de Duplicação, Regras de Correspondência, Flows)
```

**Nenhum arquivo é alterado sem que você veja o diff e confirme.**

> **Dica: já tem um mapeamento prévio?**
>
> Se você já sabe quais classes/arquivos são impactados — seja por análise própria, documentação interna ou uma execução anterior — passe essa informação junto ao chamar o prompt. O agente usará seu mapeamento como ponto de partida e complementará com a varredura automática.
>
> Exemplo de como chamar:
> ```
> /cnpj-migration-generic
>
> Contexto adicional: as seguintes classes já foram mapeadas como impactadas:
> - force-app/main/default/classes/AccountBO.cls (valida CNPJ no método validate)
> - force-app/main/default/classes/CnpjUtils.cls (lógica central de validação)
> - force-app/main/default/lwc/cnpjInput/cnpjInput.js (máscara do campo)
> ```
>
> O agente priorizará esses arquivos na FASE 0 e ainda varrará o restante do projeto para garantir cobertura total.

---

## Como os arquivos se conectam

```
Você chama /cnpj-migration-generic
        │
        ├── lê → cnpj-alphanumerico.agent.md   (regras do CNPJ: algoritmo, formato, regex)
        ├── lê → salesforce-apex.agent.md       (convenções de código do seu projeto)
        ├── lê → salesforce-project.prompt.md   (padrões de busca — seções 1-4)
        │                   ↓
        │           varre os arquivos do projeto
        │                   ↓
        └── escreve → salesforce-project.prompt.md  (seção 5: mapa de impacto)
                            ↓ você confirma
                    executa a migração arquivo por arquivo
```

---

## Novo Formato do CNPJ — Referência Rápida

| Parte | Posições | Tipo |
|---|---|---|
| Raiz | 1–8 | `[A-Z0-9]` |
| Ordem | 9–12 | `[A-Z0-9]` (Matriz = sempre `0001`) |
| DV | 13–14 | `[0-9]` (sempre numérico) |

Schema completo: `[A-Z0-9]{12}[0-9]{2}`

Exemplos reais (simulador oficial da RFB):
```
CT.G3P.N2M/0001-22  ← MATRIZ
CT.G3P.N2M/9EHH-87  ← Filial
CT.G3P.N2M/VR52-03  ← Filial
```

CNPJs numéricos antigos continuam **100% válidos para sempre**. Os sistemas devem aceitar ambos os formatos simultaneamente.

---

## Contribuindo

Se você encontrar um novo padrão relacionado a CNPJ em um projeto Salesforce que não está coberto pelas seções 1–4 de `salesforce-project.prompt.md`, abra um PR adicionando o padrão. Não remova entradas existentes.

---

## Licença

MIT
