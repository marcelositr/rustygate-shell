# AI Prompter --- RustyGate (Versão Corrigida e Human-Readable)

Este arquivo serve como *prompter técnico* para descrição, análise e
documentação do projeto **RustyGate**, já com as correções aplicadas
referentes a parser seguro, ambiente zerado, execução kernel-only e
regras estritas.

------------------------------------------------------------------------

## 🏷️ Identificação do Projeto

-   **Nome:** RustyGate\
-   **Tipo:** Shell Restrito (REPL Seguro)\
-   **Versão:** 1.0 (MVP)\
-   **Linguagem:** Rust\
-   **Sistema Alvo:** Linux POSIX (Debian 12 e derivados)\
-   **Objetivo:** Controle absoluto de comandos permitidos + auditoria
    inviolável\
-   **Ponto Forte:** Zero injeção, zero shell intermediário, execução
    limpa e segura

------------------------------------------------------------------------

## 📌 Requisitos Funcionais (MVP)

### R_F\_01 --- REPL Completo

Loop interativo seguro (Read → Parse → Validate → Execute).

### R_F\_02 --- Whitelist Obrigatória

Todos os comandos devem existir no `rules.toml`.

### R_F\_03 --- Execução Segura

Usar `std::process::Command` (fork/exec) SEM shell no meio.

### R_F\_04 --- Comandos Suportados no MVP

-   ls\
-   pwd\
-   cd (builtin confinado)\
-   cat\
-   mkdir\
-   rmdir\
-   echo

### R_F\_05 --- Negação Explícita

Qualquer comando não listado → NEGADO + LOG.

------------------------------------------------------------------------

## 🔐 Requisitos Não Funcionais

### R_NF_01 --- Imunidade à Injeção

Parser bloqueia: - `;`\
- `|`\
- `||`\
- `&`, `&&`\
- `$()`\
- backticks\
- redirecionamento (`>`, `<`, `>>`)\
- caracteres de controle\
- paths maliciosos\
- flags não listadas

### R_NF_02 --- Auditoria Profissional

Logar tudo no SYSLOG (`LOG_AUTH`), incluindo: - comando tentado\
- flags\
- UID\
- resultado

### R_NF_03 --- Performance

Validação abaixo de 100ms.

### R_NF_04 --- Compatibilidade POSIX

Sem dependências esquisitas, sem treta com distros.

------------------------------------------------------------------------

## 🧩 Arquitetura Modular (Rust)

### 1. `main.rs` --- Orquestrador do REPL

-   inicializa ambiente\
-   gerencia input\
-   chama parser → executor → logger

------------------------------------------------------------------------

### 2. `config.rs` --- Parser do TOML

-   carrega `rules.toml`\
-   usa `serde` + `toml`\
-   flags estritas\
-   opcional: `allow_users = ["uid"]`

------------------------------------------------------------------------

### 3. `parser.rs` --- Tokenizador Blindado

Correções aplicadas:

✔ Tokenização por espaço\
✔ ASCII seguro apenas\
✔ Bloqueio de caracteres perigosos\
✔ Sem `..` no cd\
✔ Sem `/` absoluto\
✔ Sem symlinks maliciosos\
✔ Validação estrita de flags\
✔ Normalização de erro\
✔ Retorno de `ValidatedCommand`

------------------------------------------------------------------------

### 4. `executor.rs` --- Execução Kernel-only

-   Zera ambiente\
-   Reconstrói apenas:

```{=html}
<!-- -->
```
    PATH=/bin:/usr/bin
    LANG=C
    LC_ALL=C
    HOME=/home/restrito

-   Passa argumentos como vetor\
-   Sem shell, sem interpretação

------------------------------------------------------------------------

### 5. `logger.rs` --- Auditoria Invencível

-   Logs enviados ao `LOG_AUTH`\
-   Usuário não pode tocar nos logs\
-   Integração com rotação/retention do rsyslog

------------------------------------------------------------------------

## 🛠️ Pontos Corrigidos

### ✔ Parser reforçado

Zero brecha para injeção.

### ✔ cd confinado

Diretório-base seguro, sem fuga.

### ✔ Flags estritas

Nada de flag doida ou combinação proibida.

### ✔ Ambiente limpo

Nada de variável maliciosa influenciando binários.

### ✔ Execução kernel-only

Sem risco de shell magic.

### ✔ Logs seguros

Sem tampering, auditoria completa.

------------------------------------------------------------------------

## 🧭 Roadmap Futuro

1.  Variáveis internas seguras\
2.  Pipes restritos e validados\
3.  Regras por UID/GID\
4.  Histórico seguro\
5.  Comandos virtuais customizados

------------------------------------------------------------------------

## 📄 Finalização

Este prompter descreve o RustyGate com as melhorias implementadas,
garantindo máxima segurança e um comportamento consistente, controlado e
auditável para ambientes críticos.
