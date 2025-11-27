# RustyGate --- Shell Restrito em Rust (Versão Humana)

O **RustyGate** é um shell restrito, seguro e totalmente customizável,
projetado para substituir o shell padrão de usuários com acesso
limitado.\
Ele funciona como um **porteiro blindado**: só deixa passar comandos que
você autorizou no `rules.toml`.

------------------------------------------------------------------------

## 🎯 Objetivo do Projeto

Criar um **shell real**, seguro, minimalista e auditável, usando Rust
para garantir: - Zero injeção - Zero gambiarra - Zero execução não
autorizada - Auditoria completa via Syslog

------------------------------------------------------------------------

## 🔐 Conceito Principal

O RustyGate é um REPL (Read--Eval--Print Loop) com **validação rígida**:

1.  Usuário digita um comando\
2.  O parser quebra em tokens e valida\
3.  Verifica whitelist\
4.  Executa via `Command` (kernel-only, sem shell)\
5.  Loga tudo no Syslog

Se não estiver na whitelist → NEGADO + LOG.

------------------------------------------------------------------------

## 🧩 Arquitetura do Sistema

### `main.rs` --- Núcleo REPL

Gerencia: - leitura interativa\
- fluxo de validação\
- respostas de erro\
- prompt seguro

------------------------------------------------------------------------

### `config.rs` --- Carregamento do TOML

-   Lê o arquivo `rules.toml`\
-   Usa `serde`\
-   Mantém tudo em estrutura imutável\
-   Define comandos permitidos e flags autorizadas

Exemplo:

``` toml
[[command]]
name = "ls"
path = "/bin/ls"
allow_flags = ["-l", "-a"]
allow_users = ["1001"]
```

------------------------------------------------------------------------

### `parser.rs` --- Tokenização Blindada

Correções aplicadas neste módulo: - bloqueia caracteres perigosos (`;`,
`|`, `&&`, `$()`, etc.)\
- quebra em tokens apenas pelos espaços\
- valida ASCII seguro\
- impede caminhos maliciosos\
- valida flags estritamente com o TOML\
- tratamento uniforme de erro

Resultado: um `ValidatedCommand`.

------------------------------------------------------------------------

### `executor.rs` --- Execução Segura

-   Zero interpretação de shell\
-   Usa `std::process::Command`\
-   Zera variáveis de ambiente\
-   Reconstrói ambiente mínimo:

```{=html}
<!-- -->
```
    PATH=/bin:/usr/bin
    LANG=C
    LC_ALL=C
    HOME=/home/restrito

------------------------------------------------------------------------

### `logger.rs` --- Auditoria Profissional

-   Envia logs para `LOG_AUTH`\
-   Registra: comando, flags, UID, hora e resultado\
-   Usuário restrito **não pode** modificar o log\
-   Rotação garantida pelo rsyslog

------------------------------------------------------------------------

## 🏗️ Comandos Suportados (MVP)

Comandos aceitos **apenas se listados no TOML**:

-   `ls`
-   `pwd`
-   `cd` (builtin seguro e enjaulado)
-   `cat`
-   `mkdir`
-   `rmdir`
-   `echo`

------------------------------------------------------------------------

## 🛡️ Segurança Aplicada (Correções Inclusas)

### ✔ Parser reforçado

Sem espaço pra injeção.\
Só ASCII limpo.\
Flags estritamente controladas.

### ✔ `cd` confinado

Caminhos: - sem `..` - sem `/` absoluto - sem links simbólicos\
- só funciona dentro do diretório-base do usuário

### ✔ Execução kernel-only

Nada de: - pipes\
- backticks\
- subshell\
- redirecionamento\
- expansões mágicas

### ✔ Ambiente zerado

Nada de variáveis maliciosas influenciando execução.

### ✔ Logs invioláveis

Rastreabilidade total.

------------------------------------------------------------------------

## 🧭 Roadmap Futuro

-   Variáveis seguras internas\
-   Pipes controlados\
-   Permissões por UID/GID\
-   Histórico seguro\
-   Comandos virtuais customizados

------------------------------------------------------------------------

## 📌 Conclusão

O RustyGate não é "um bash pobre".\
Ele é um **shell restrito profissional**, projetado para segurança real,
ambientes críticos e controle total.

Segurança máxima.\
Controle absoluto.\
Rust até os ossos.

------------------------------------------------------------------------

## 📄 Autor

Projeto criado por **Marcelo da Silva Trindade (marcelositr)**.\
Auxiliado espiritualmente pelo **Cachoeira** 😎
