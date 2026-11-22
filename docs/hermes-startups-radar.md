# Hermes Startups Radar no WSL

Registro atualizado em **22 de novembro de 2026**.

Este guia acompanha uma instalação local do Hermes para usar os métodos do Radar Startups e Tecnologias Emergentes. A instalação foi feita no Ubuntu do WSL, separada do Codex, do Camaleão e do Hermes existente na VPS.

O runtime foi instalado, o login próprio foi confirmado e o modelo foi selecionado. **Ainda não houve uma resposta bem-sucedida do Hermes por inferência.** Uma sonda offline conferiu esquemas, e uma tentativa curta de resposta parou na validação local. A base privada ainda não foi transferida para o PC, e o novo agente ainda não foi criado na plataforma Radar.

## O que foi confirmado

As observações do PC vieram das saídas do Ubuntu fornecidas durante a instalação assistida.

| Item | Estado observado |
| --- | --- |
| Código Hermes | Commit `d236cbe069577679a2d3b60c42bd58e1fe879ca1` |
| Aquisição do código | Cópia rasa do commit exato, após falha na clonagem do histórico |
| Dependências | Etapas do instalador concluídas |
| Identidade exibida | `vgit.d236cbe` |
| Python do Hermes | `3.14.9`, gerenciado pelo projeto |
| uv | `0.13.5` |
| OpenAI SDK | `2.54.0` |
| Configuração separada | `$HOME/.hermes-radar-startups/config.yaml` |
| Login Hermes | `openai-codex: logged in` |
| Login armazenado do Codex | Continuou indicando conexão pelo ChatGPT |
| Modelo salvo | `gpt-6.2-sol`, via OpenAI Codex |
| Raciocínio salvo | `medium` |
| Radar existente | Escuta local na porta `3100` confirmada |
| Sonda offline dos esquemas | Retornou apenas `clarify`, sem chamar o modelo |
| Tentativa curta de resposta | Recusada localmente com `invalid_params` |
| Resposta por inferência no perfil normal | Pendente |
| Novo agente na plataforma Radar | Pendente |

A mensagem de autenticação confirma o login. Ela não comprova cota disponível, elegibilidade do modelo escolhido ou execução completa do Camaleão. Selecionar um modelo também não comprova que ele respondeu.

A cópia rasa não traz o histórico e as tags de release. Por isso a instalação pode se identificar pelo Git, sem uma versão semântica de release. Não criar tags ou versões falsas para mudar essa identificação.

## Como a solução será organizada

Começar com um coordenador, **Hermes Startups Radar**, e cinco percursos de trabalho:

| Percurso | Modos da habilidade de origem |
| --- | --- |
| Produto e Validação | Diagnóstico e validar |
| Estratégia e Mercado | Estratégia e mercado |
| Finanças e Captação | Financeiro, captação e pitch |
| Tecnologia e Evidências | Tecnologia |
| Execução | Execução |

Os nove modos pertencem à habilidade privada. Seus nomes com barra são atalhos de método, não comandos nativos do Hermes já implementados.

Esses percursos ainda não são cinco processos nem cinco agentes independentes. O coordenador será criado e testado antes de qualquer expansão.

## Requisitos e separação de pastas

O procedimento usa Git, curl, tar, SHA-256 e um Ubuntu do WSL com acesso aos servidores de dependências. O gerenciador do Hermes prepara seu próprio runtime. Não é necessário trocar o Python ou o Node global apenas para acompanhar as versões internas.

| Uso | Caminho proposto |
| --- | --- |
| Código | `$HOME/projetos/hermes-radar-startups` |
| Dados, configuração e login próprios | `$HOME/.hermes-radar-startups` |
| Trabalho do piloto | `$HOME/projetos/radar-startups-lab` |
| Preparação e backups privados | Uma pasta própria dentro de `$HOME/projetos` |

Use sempre o executável específico da instalação nova e seu `HERMES_HOME`. Não copie `~/.codex/auth.json` e não reutilize o `CODEX_HOME` temporário de outro agente.

## 1. Inventariar antes de instalar

Se estiver no PowerShell, entre primeiro no Ubuntu:

```powershell
wsl -d Ubuntu
```

Execute os próximos blocos no terminal do Ubuntu, não no PowerShell.

No Ubuntu:

```bash
printf 'Pasta pessoal: %s\n' "$HOME"
uname -sr
df -h "$HOME"
for ferramenta in git curl python3 node hermes docker bwrap codex; do
  if command -v "$ferramenta" >/dev/null 2>&1; then
    command -v "$ferramenta"
  fi
done
ss -H -ltn 'sport = :3100'
```

Confira os diretórios de destino antes de gravar. Se já houver uma instalação, configuração ou launcher `hermes`, preserve-os e prepare uma recuperação específica. Não rode a sequência de instalação nova sobre um diretório desconhecido.

Antes da instalação acompanhada, foram guardadas cópias privadas dos arquivos de shell e do estado anterior da pasta de dados. O instalador pode publicar um comando global e alterar o PATH em arquivos como `.bashrc` e `.profile`. Esses efeitos precisam fazer parte do inventário e do backup.

Para guardar os arquivos existentes em uma pasta privada, sem removê-los:

```bash
(
set -eu
umask 077
mkdir -p "$HOME/projetos"
backup="$(mktemp -d "$HOME/projetos/hermes-startups-backup.XXXXXXXX")"
itens=()
for item in .bashrc .profile .bash_profile .bash_login .local/bin/hermes .hermes-radar-startups; do
  if [ -e "$HOME/$item" ] || [ -L "$HOME/$item" ]; then
    itens+=("$item")
  fi
done
if [ "${#itens[@]}" -gt 0 ]; then
  tar -C "$HOME" -cpf "$backup/antes.tar" "${itens[@]}"
fi
printf 'Pasta de backup privado: %s\n' "$backup"
)
```

A cópia não autoriza sobrescrever uma instalação existente. Confira o estado antes de escolher como prosseguir.


Não publique backups, logs completos, e-mails, códigos de login ou arquivos de autenticação em issues e PRs.

## 2. Obter e conferir o instalador fixado

Use uma pasta de preparação nova. O exemplo abaixo recusa um destino já existente:

```bash
(
set -eu
umask 077
mkdir -p "$HOME/projetos"
preparo="$HOME/projetos/hermes-startups-preparo"
test ! -e "$preparo"
mkdir -m 700 "$preparo"
curl --fail --show-error --location \
  'https://raw.githubusercontent.com/ofelipepeixoto/hermes-agent/d236cbe069577679a2d3b60c42bd58e1fe879ca1/scripts/install.sh' \
  --output "$preparo/install.sh"
printf '%s  %s\n' \
  'c73dad4dcf7c9ecadb98578e28cc763d23d7be3fa1b513545319038795493214' \
  "$preparo/install.sh" | sha256sum --check -
bash -n "$preparo/install.sh"
)
```

O hash registra a identidade do arquivo. Leia também o script e suas dependências. Não executar um download diretamente por um pipe para Bash.

## 3. Obter somente o commit escolhido

A clonagem inicial falhou durante a transferência do histórico, com `curl 56`, `early EOF` e erro de `index-pack`. O instalador possui tentativas automáticas. É preciso esperar que ele encerre antes de iniciar outra aquisição.

Depois de confirmar que a tentativa anterior terminou, a recuperação usada foi baixar um único commit:

```bash
(
set -eu
umask 077
destino="$HOME/projetos/hermes-radar-startups"
commit=d236cbe069577679a2d3b60c42bd58e1fe879ca1
test ! -e "$destino"
test ! -L "$destino"
unset GIT_DIR GIT_WORK_TREE GIT_INDEX_FILE GIT_COMMON_DIR GIT_OBJECT_DIRECTORY GIT_ALTERNATE_OBJECT_DIRECTORIES
fonte="$(mktemp -d "$HOME/projetos/.hermes-startups-fonte.XXXXXXXX")"
printf 'Pasta temporária: %s\n' "$fonte"
git -C "$fonte" init --quiet
git -C "$fonte" remote add origin https://github.com/ofelipepeixoto/hermes-agent.git
GIT_TERMINAL_PROMPT=0 timeout --kill-after=15s 900s \
  git -C "$fonte" -c http.version=HTTP/1.1 -c fetch.fsckObjects=true \
  fetch --depth=1 --no-tags origin "$commit"
timeout --kill-after=15s 300s git -C "$fonte" checkout --detach FETCH_HEAD
test "$(git -C "$fonte" rev-parse HEAD)" = "$commit"
test ! -e "$destino"
test ! -L "$destino"
mv -T "$fonte" "$destino"
git -C "$destino" rev-parse HEAD
sha256sum "$destino/pm/lock.json" "$destino/uv.lock"
)
```

A opção HTTP/1.1 vale somente para essa chamada. Não altera configurações globais, não desativa TLS e não garante corrigir toda falha de rede.

Hashes observados na instalação acompanhada:

| Arquivo | SHA-256 |
| --- | --- |
| `pm/lock.json` | `aa3617e66ab2b69fd08402e49e633c78bd030c16d5c62610e04a6216605b63f3` |
| `uv.lock` | `d0ae8bfd20db7a8ca0e75418df718315c58088b120366227cc62ee8fe25d4274` |

Se uma etapa falhar, preserve o resultado para diagnóstico. Não apague diretórios desconhecidos ou outras cópias do projeto para tentar novamente.

## 4. Criar a configuração restrita antes do login

Para uma instalação nova, crie o diretório de dados com permissão privada e grave os arquivos de configuração sem sobrescrever arquivos existentes. O exemplo é JSON válido como YAML e usa somente campos conferidos no commit escolhido:

```bash
python3 - <<'PY'
from pathlib import Path
import json
import os

raiz = Path.home() / ".hermes-radar-startups"
raiz.mkdir(mode=0o700, exist_ok=False)
config = {
    "toolsets": ["clarify"],
    "max_concurrent_sessions": 1,
    "auth": {"adopt_external_logins": False},
    "agent": {"disabled_toolsets": ["memory"]},
    "memory": {
        "memory_enabled": False, "user_profile_enabled": False,
        "provider": "", "write_approval": True
    },
    "skills": {
        "project_discovery": False, "inline_shell": False, "auto_load": [],
        "write_approval": True, "guard_agent_created": True
    },
    "curator": {"enabled": False},
    "auxiliary": {
        "free_only": True,
        "background_review": {"enabled": False},
        "title_generation": {"enabled": False, "model_upgrade_enabled": False},
        "compression": {"provider": "openai-codex"}
    },
    "fallback_providers": [],
    "approvals": {
        "mode": "manual", "cron_mode": "deny",
        "single_query_mode": "deny", "unattended_mode": "deny"
    },
    "telemetry": {"shared_metrics": {"enabled": False, "send": False}},
    "timezone": "America/Sao_Paulo"
}
for nome, texto in [
    ("config.yaml", json.dumps(config, ensure_ascii=False, indent=2) + "\n"),
    (".env", "")
]:
    fd = os.open(raiz / nome, os.O_WRONLY | os.O_CREAT | os.O_EXCL, 0o600)
    with os.fdopen(fd, "w") as arquivo:
        arquivo.write(texto)
PY
```

Se a pasta já existir, não repita esse bloco. Confira o que há nela, guarde o estado anterior e use uma adaptação que preserve a configuração vigente.

Essa configuração inicial foi conferida pela CLI durante a instalação. Ela ainda precisa ser reconferida depois de mudanças relevantes, incluindo a seleção do modelo.

## 5. Instalar pelas etapas suportadas

Na recuperação com cópia rasa, não executar novamente o estágio `repository`: ele buscaria a branch e dependeria de uma ancestralidade que a cópia rasa não contém.

As etapas usadas foram:

```text
prerequisites
venv
python-deps
config
products
complete
```

Não foram executados `setup` ou `gateway`. O login foi feito depois, de forma própria e supervisionada.

Use o instalador conferido na etapa 2. Se ele estiver em outra pasta, ajuste somente a variável correspondente:

```bash
(
set -eu
umask 077
codigo="$HOME/projetos/hermes-radar-startups"
instalador="$HOME/projetos/hermes-startups-preparo/install.sh"
commit=d236cbe069577679a2d3b60c42bd58e1fe879ca1
unset GIT_DIR GIT_WORK_TREE GIT_INDEX_FILE GIT_COMMON_DIR GIT_OBJECT_DIRECTORY GIT_ALTERNATE_OBJECT_DIRECTORIES
test "$(git -C "$codigo" rev-parse HEAD)" = "$commit"
git -C "$codigo" diff --quiet
git -C "$codigo" diff --cached --quiet
printf '%s  %s\n' \
  'c73dad4dcf7c9ecadb98578e28cc763d23d7be3fa1b513545319038795493214' \
  "$instalador" | sha256sum --check -
for etapa in prerequisites venv python-deps config products complete; do
  test "$(git -C "$codigo" rev-parse HEAD)" = "$commit"
  printf '\nEtapa: %s\n' "$etapa"
  timeout --kill-after=30s 1200s env -i \
    HOME="$HOME" USER="$USER" LOGNAME="$USER" SHELL=/bin/bash \
    PATH=/usr/local/bin:/usr/bin:/bin LANG=C.UTF-8 \
    HERMES_HOME="$HOME/.hermes-radar-startups" \
    HERMES_REPO_URL=https://github.com/ofelipepeixoto/hermes-agent.git \
    bash "$instalador" \
    --branch main --commit "$commit" \
    --dir "$codigo" --hermes-home "$HOME/.hermes-radar-startups" \
    --non-interactive --skip-browser --skip-computer-use \
    --stage "$etapa"
done
)
```

O bloco para na primeira falha. Só chamar `complete` quando as etapas anteriores passarem.

### Se houver falha de DNS

Durante o preparo de `uv`, houve `curl: (6) Could not resolve host` para dois servidores. Um diagnóstico posterior mostrou que ambos voltaram a resolver e que a porta local do Radar continuava ouvindo.

A retomada começou em `venv`, sem repetir a clonagem nem recriar a configuração. Não foi necessário alterar DNS ou reiniciar o WSL nessa ocorrência.

Para diagnóstico de leitura:

```bash
for servidor in github.com hermes-assets.nousresearch.com; do
  printf '\nServidor: %s\n' "$servidor"
  timeout 12s getent ahostsv4 "$servidor"
  printf 'Resultado DNS: %s\n' "$?"
done
ss -H -ltn 'sport = :3100'
```

A recuperação observada não prova que toda falha de DNS se resolva da mesma forma. Se os nomes continuarem sem resposta, registrar o bloqueio e investigar a rede antes de repetir downloads.

## 6. Conferir instalação e controles

O launcher esperado nesta instalação é:

```text
$HOME/projetos/hermes-radar-startups/.hermes/bin/hermes
```

Faça a conferência com o estado próprio:

```bash
(
set -eu
executavel="$HOME/projetos/hermes-radar-startups/.hermes/bin/hermes"
test -x "$executavel"
hermes_piloto() {
  timeout --kill-after=10s 60s env -i \
    HOME="$HOME" USER="$USER" LOGNAME="$USER" SHELL=/bin/bash \
    PATH=/usr/local/bin:/usr/bin:/bin LANG=C.UTF-8 \
    HERMES_HOME="$HOME/.hermes-radar-startups" HERMES_SAFE_MODE=1 \
    "$executavel" "$@"
}
hermes_piloto --version
hermes_piloto config path
for chave in auth.adopt_external_logins toolsets memory.memory_enabled memory.user_profile_enabled curator.enabled auxiliary.background_review.enabled auxiliary.title_generation.enabled auxiliary.free_only fallback_providers max_concurrent_sessions; do
  printf '\n%s\n' "$chave"
  hermes_piloto config get "$chave" --json
done
)
```

Resultados observados antes da seleção do modelo:

| Controle | Valor |
| --- | --- |
| Adoção automática de logins externos | `false` |
| Toolsets configurados | `["clarify"]` |
| Memória e perfil persistentes | `false` |
| Curator, revisão em segundo plano e geração de títulos | `false` |
| Restrição auxiliar `free_only` | `true` |
| Provedores de reserva | `[]` |
| Concorrência | `1` |

Isso confere a configuração lida naquele momento. **A lista de ferramentas efetivamente apresentada ao modelo em uma sessão normal ainda precisa ser inspecionada.**

### Sonda offline posterior

Uma sonda executada no Python 3.14.9 do ambiente gerenciado retornou os seguintes campos:

| Campo observado | Resultado |
| --- | --- |
| `tools` | `["clarify"]` |
| `model_called` | `false` |
| `guard_refusals` | Uma ocorrência de `socket.__new__` bloqueada |

O traço `family=10`, `type=1`, `proto=0` é compatível com uma checagem IPv6 durante importação. Ele não identifica o pacote responsável nem prova conexão ou envio de dados.

Essa é uma conferência dos esquemas sob bloqueios de inspeção. O audit hook Python não substitui isolamento no sistema operacional, não cobre todo código nativo e não comprova o comportamento de uma sessão normal. A ativação do ambiente também pode gravar locks, leases e cache próprios.

## 7. Fazer um login próprio

O procedimento usado foi:

```bash
env -i \
  HOME="$HOME" USER="$USER" LOGNAME="$USER" SHELL=/bin/bash \
  PATH=/usr/local/bin:/usr/bin:/bin LANG=C.UTF-8 \
  HERMES_HOME="$HOME/.hermes-radar-startups" HERMES_SAFE_MODE=1 \
  timeout --kill-after=10s 600s \
  "$HOME/projetos/hermes-radar-startups/.hermes/bin/hermes" \
  auth add openai-codex --type oauth \
  --label "Hermes Startups Radar" --no-browser
```

Abra o endereço oficial mostrado no terminal e conclua a autorização na sua própria conta. Digite o código apenas na página da OpenAI. Não cole códigos ou tokens em conversas, issues ou relatórios públicos.

O estado foi conferido com:

```bash
env -i \
  HOME="$HOME" USER="$USER" LOGNAME="$USER" SHELL=/bin/bash \
  PATH=/usr/local/bin:/usr/bin:/bin LANG=C.UTF-8 \
  HERMES_HOME="$HOME/.hermes-radar-startups" HERMES_SAFE_MODE=1 \
  timeout --kill-after=10s 60s \
  "$HOME/projetos/hermes-radar-startups/.hermes/bin/hermes" \
  auth status openai-codex
codex login status
```

O Hermes informou login próprio ativo, e o Codex continuou indicando login pelo ChatGPT. Isso não substitui um teste completo do agente Camaleão.

Não criar chave API, habilitar créditos ou mudar para outro provedor como forma automática de contornar um erro. Limites e acesso à rota de assinatura ainda dependem da conta e do teste real.

## 8. Selecionar o modelo

Com o login próprio concluído:

```bash
env -i \
  HOME="$HOME" USER="$USER" LOGNAME="$USER" SHELL=/bin/bash \
  PATH=/usr/local/bin:/usr/bin:/bin LANG=C.UTF-8 TERM="${TERM:-xterm-256color}" \
  HERMES_HOME="$HOME/.hermes-radar-startups" HERMES_SAFE_MODE=1 \
  timeout --kill-after=10s 600s \
  "$HOME/projetos/hermes-radar-startups/.hermes/bin/hermes" model
```

Na instalação acompanhada, o percurso foi:

1. **OpenAI**.
2. **ChatGPT or Codex Subscription**.
3. **Use existing credentials**, usando o login próprio que acabara de ser confirmado.
4. **gpt-6.2-sol**.

O terminal salvou `gpt-6.2-sol`, via OpenAI Codex, com raciocínio `medium`. A presença no menu e o salvamento não comprovam acesso real ou cota. O próximo passo é uma chamada pequena e controlada.

### Tentativa curta de resposta

Uma tentativa de resposta curta, com pedido de `OK`, recebeu `invalid_params` durante a preparação local. O comando orientado pelo assistente usava `HERMES_SAFE_MODE=1` sem informar um modelo explicitamente.

A leitura do mesmo commit identificou que essa variável ativa um caminho de bypass que não carrega a configuração do perfil. Sem um modelo explícito nesse caminho, a validação `_bypass_policy`, em [gateway/session_local.py](https://github.com/ofelipepeixoto/hermes-agent/blob/d236cbe069577679a2d3b60c42bd58e1fe879ca1/gateway/session_local.py), recusa a sessão localmente antes da chamada ao modelo.

O operador executou o comando que recebeu. A correção é do procedimento orientado pelo assistente, sem patch no runtime. O resultado não estabelece falha da assinatura, das credenciais ou da conta.

Os comandos de configuração e autenticação acima registram o percurso executado. Não reutilizar `HERMES_SAFE_MODE=1` como se ele preservasse o perfil de uma sessão normal. Antes da próxima chamada, conferir o carregamento do perfil e os controles de plugins, hooks e MCP, mantendo os limites de tempo, turnos e ferramentas.

A chamada corrigida com o perfil normal ainda não foi executada. O primeiro resultado por inferência continua pendente.

## 9. Transferir a base privada

O repositório público contém o guia. Ele não contém a habilidade completa nem os documentos empresariais originais.

Na preparação privada, a versão 0.1.1 foi conferida:

- 44 arquivos Markdown.
- 189.779 bytes.
- 362 links relativos verificados, sem destinos ausentes ou fora da habilidade.
- Cópia byte a byte e importador com oito testes em pastas temporárias.

Esses resultados pertencem ao ambiente de preparação. **Ainda não houve confirmação de transferência da base para o WSL.**

A transferência futura deve usar o pacote autorizado por canal privado, preservar a árvore completa e conferir os hashes antes da importação. Não publicar o ZIP, o conteúdo da base ou links privados neste repositório.

Os métodos estudados não substituem os dados reais de cada startup. Exemplos e projeções de terceiros não devem virar resultados do projeto em análise.

## 10. Critérios para avançar

| Próxima etapa | Critério de conclusão |
| --- | --- |
| Inspeção das ferramentas | A sonda offline conferiu esquemas; falta conferir as ferramentas da sessão normal |
| Primeiro teste | Repetir a chamada com o perfil normal e controles verificados; obter uma resposta pela assinatura |
| Base no WSL | Transferir e conferir a árvore e os hashes |
| Consulta ao acervo | Recuperar referências corretas e reconhecer fontes ausentes |
| Casos fictícios | Conferir LTV, caixa, compromisso pago, projeção versus tração e plano de execução |
| Controles negativos | Verificar recusa de ações fora do escopo, preservação da base e separação entre casos |
| Isolamento | Testar a separação do processo inteiro antes de conteúdo externo não confiável ou contas reais |
| Novo agente Radar | Criar somente o coordenador, se o adaptador instalado for compatível |
| Tarefa na plataforma | Resposta registrada e disposição final confirmada |
| Recuperação | Restaurar backup em outro diretório, preservando a instalação ativa |

Houve uma tentativa curta, recusada na validação local. Não há resposta por inferência, novo agente Radar, restauração ou isolamento integral confirmado nesta etapa.

O Docker estava instalado, mas seu servidor não foi confirmado no diagnóstico. Bubblewrap foi encontrado, porém seu funcionamento como isolamento do processo inteiro ainda não foi testado.

## Limites de segurança

- Perfis, nomes de agentes e pastas separadas organizam o estado. Não criam isolamento do sistema operacional.
- Docker usado apenas pelo terminal não isola automaticamente todos os caminhos do processo Hermes.
- `toolsets: ["clarify"]` é a configuração observada, e a sonda offline retornou esse esquema. Falta confirmar o conjunto de uma sessão normal.
- O bloqueio Python da sonda é um controle de inspeção, não uma sandbox do sistema operacional.
- O toolset `skills` também inclui escrita. Não tratá-lo como somente leitura.
- Permissões de leitura aplicadas pelo mesmo dono podem ser revertidas por esse dono.
- Desligar memória não elimina sessões, histórico, logs, cache e propostas pendentes.
- `free_only` sozinho não comprova que toda chamada usará a assinatura. Conferir a rota principal e as auxiliares.
- Não houve varredura ativa com Garak ou Strix nem homologação de produção.

A revisão de leitura do commit também considerou o alerta [GHSA-p22p-9ph6-gmrh](https://github.com/NousResearch/hermes-agent/security/advisories/GHSA-p22p-9ph6-gmrh). As duas partes centrais da correção foram observadas nessa referência. Isso não equivale a uma auditoria completa das dependências.

## Recuperação

A documentação da instalação deve identificar os backups privados e os arquivos realmente modificados. Para desfazer:

1. Pausar somente o novo agente e impedir novos acionamentos.
2. Parar apenas os processos do piloto identificados.
3. Restaurar os arquivos de shell ou launchers necessários a partir do backup conferido.
4. Revogar apenas o login próprio do Hermes, se o piloto for descontinuado.
5. Guardar ou remover os dados do piloto conforme decisão do operador.
6. Conferir que Radar, Codex, Camaleão e o Hermes da VPS continuam no estado esperado.

Não encerrar todos os processos Node, apagar diretórios por padrão ou restaurar um backup sobre uma instalação em execução.

## Fontes

- [Runtime fixado](https://github.com/ofelipepeixoto/hermes-agent/tree/d236cbe069577679a2d3b60c42bd58e1fe879ca1).
- [Instalador fixado](https://github.com/ofelipepeixoto/hermes-agent/blob/d236cbe069577679a2d3b60c42bd58e1fe879ca1/scripts/install.sh).
- [Política de segurança do runtime](https://github.com/ofelipepeixoto/hermes-agent/blob/d236cbe069577679a2d3b60c42bd58e1fe879ca1/SECURITY.md).
- [Configuração do runtime](https://github.com/ofelipepeixoto/hermes-agent/blob/d236cbe069577679a2d3b60c42bd58e1fe879ca1/hermes_cli/config_defaults.py).
- [Documentação do adaptador Hermes local](https://docs.paperclip.ing/reference/adapters/hermes/).
- Registros privados da instalação assistida e da preparação da habilidade 0.1.1.

O Hermes mantém a licença e as atribuições da Nous Research. Este guia não altera a licença do runtime e não atribui à base privada uma permissão de redistribuição pública.

[Roadmap](../ROADMAP.md) · [Histórico](../CHANGELOG.md) · [Voltar ao início](../README.md)

