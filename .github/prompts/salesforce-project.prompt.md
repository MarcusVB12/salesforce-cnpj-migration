---
description: "Catálogo de padrões de busca e regras de classificação para migração CNPJ em projetos Salesforce. Lido e preenchido pelo cnpj-migration-generic: as seções 1-4 são estáticas (regras universais para qualquer projeto Salesforce), a seção 5 é gerada automaticamente pelo agente após escanear o projeto atual."
---

# Padrões de Busca CNPJ — Projetos Salesforce

> **Seções 1 a 4** — conhecimento estático, válido para qualquer projeto Salesforce. Não altere manualmente(CASO PRECISE).
> **Seção 5** — gerada automaticamente pelo prompt `cnpj-migration-generic` após escanear o projeto. Reescrita a cada execução de discovery.

---

## 1. Padrões de texto a buscar (case-insensitive)

### Identificadores de campo / variável
- `CNPJ`
- `cnpj`
- `cpf_cnpj`
- `CPF_CNPJ`

### Funções e métodos
- `formatCNPJ`
- `validCNPJ`
- `validateCNPJ`
- `isCNPJ`
- `cnpjValido`
- `cnpjMask`
- `maskCNPJ`
- `normalizeCNPJ`
- `cleanCNPJ`

### Nomes de classe / componente
- `BrazilianDocument`
- `DocumentUtils`
- `DocumentValidator`
- `Sintegra`

---

## 2. Padrões regex a buscar em validationRules, classes e flows

| Padrão | Por que impacta | O que fazer |
|---|---|---|
| `[0-9]{14}` | Aceita só dígitos — rejeita alfanumérico | Substituir por `[A-Z0-9]{12}[0-9]{2}` |
| `\\d{14}` | Idem | Idem |
| `\d{2}\.\d{3}\.\d{3}\/\d{4}-\d{2}` | Máscara com barra fixa — falha com letras | Adaptar para `[A-Z0-9]{2}\.[A-Z0-9]{3}…` |
| `NOT REGEX(.*,[0-9]{14})` | Validação em fórmula Salesforce | Adaptar regex da fórmula |
| `REGEX(.*,[0-9]{14})` | Idem | Adaptar |
| `LEFT(CNPJ` | Extrai raiz por posição — vai mudar se raiz for alfanumérica | Revisar manualmente no Flow Builder |
| `LEFT(CNPJ__c` | Idem para campo customizado | Revisar manualmente |

---

## 3. Diretórios a escanear

| Diretório (relativo ao force-app) | O que buscar |
|---|---|
| `**/classes/` | `.cls` contendo qualquer padrão da seção 1 ou 2 |
| `**/triggers/` | `.trigger` |
| `**/objects/**/validationRules/` | `.validationRule-meta.xml` |
| `**/flows/` | `.flow-meta.xml` |
| `**/lwc/**` | `.js` com validação de formulário |
| `**/aura/**` | `*Helper.js`, `*Controller.js` |
| `**/duplicateRules/` | `.duplicateRule-meta.xml` — matching por CNPJ |
| `**/matchingRules/` | `.matchingRule-meta.xml` com campo CNPJ |
| `**/objects/**/fields/` | Campos Text com "CNPJ" no nome — verificar `length` |

---

## 4. Regras de classificação por tipo de arquivo

### CRÍTICO — bloqueiam entrada de dados (deploy deve ser o primeiro)
- `objects/**/validationRules/` com padrão CNPJ → **regra bloqueia save do registro**
- `objects/**/fields/*CNPJ*.field-meta.xml` com `length < 18` → campo muito curto para o novo formato mascarado

### HIGH — lógica de validação de negócio
- Classes com nome contendo: `Validation`, `Validator`, `Document`, `CNPJ`, `CPF`
- Classes de integração externa que enviam/recebem CNPJ (ex: Sintegra, ReceitaWS, SERPRO)
- Classes BO/Service que chamam validadores

### MEDIUM — acesso a dados / normalização / front-end
- DAO, Repository, Controller que normalizam ou filtram por CNPJ
- LWC / Aura com máscara de entrada de CNPJ

### REVISÃO MANUAL — não pode ser alterado por deploy de código
- `duplicateRules/` — precisa de reconfiguração no Setup > Duplicate Rules
- `matchingRules/` — idem, Setup > Matching Rules
- `flows/` com `LEFT(CNPJ` — validar lógica no Flow Builder
- `objects/**/fields/` — mudança de comprimento pode exigir migração de dados

---

## 5. Mapa de impacto do projeto atual

> ⚠️ Esta seção é gerada automaticamente pelo prompt `cnpj-migration-generic`.
> Não edite manualmente. O agente reescreve este bloco após o discovery e aguarda sua confirmação antes de prosseguir.

<!-- DISCOVERY_OUTPUT_START -->
_Ainda não executado. Rode `/cnpj-migration-generic` para preencher esta seção._
<!-- DISCOVERY_OUTPUT_END -->
