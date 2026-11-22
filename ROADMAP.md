# Roadmap do Hermes Startups Radar

Atualizado em 22/11/2026. Este roteiro distingue a instalação local do runtime, o uso da base privada e a integração futura ao Radar/Paperclip. Não altera a versão 0.1.1 do plugin.

| Marco | Situação | Critério de conclusão |
| --- | --- | --- |
| H0: instalar e autenticar | Concluído no WSL, com saídas fornecidas pelo operador | Fonte fixada, executável, controles selecionados e login próprio conferidos; modelo salvo |
| H1: primeiro teste de modelo | Parcial: sonda offline concluída; tentativa curta recusada localmente | Conferir ferramentas da sessão normal e obter resposta curta pela assinatura, sem fallback para API |
| H2: usar a base privada | Pendente no PC | Importar os 44 arquivos com integridade conferida e responder com referências realmente consultadas |
| H3: limitar e recuperar | Pendente | Provar isolamento do processo inteiro, recusa de escrita fora do escopo e restauração em diretório separado |
| H4: conectar ao Radar | Pendente | Confirmar adaptador local compatível, criar um coordenador e concluir uma tarefa no estado correto |
| H5: ampliar por necessidade | Proposto | Medir uso, justificar novos agentes e aprovar separadamente cada integração |

O próximo passo é H1. Salvar o nome de um modelo não comprova acesso, cota nem capacidade de concluir tarefas.

## H0: o que já foi observado

- Código Hermes no commit `d236cbe069577679a2d3b60c42bd58e1fe879ca1`.
- Instalação em uma pasta própria no Ubuntu/WSL2.
- Identidade exibida: `vgit.d236cbe`; Python 3.14.9; OpenAI SDK 2.54.0.
- Configuração própria com adoção automática de logins externos desligada.
- Seleção explícita de `clarify`, memória e tarefas de manutenção em segundo plano desligadas.
- Login próprio em `openai-codex`; o Codex CLI habitual também informou estar conectado ao ChatGPT.
- Modelo `gpt-6.2-sol` salvo via OpenAI Codex, com raciocínio `medium`.

Essas observações vieram das saídas do terminal do operador. Não houve acesso direto do assistente ao WSL.

## H1: provar a primeira chamada

1. Registrar a sonda offline já realizada e conferir as ferramentas da sessão normal, além do arquivo de configuração.
2. Conferir o carregamento do perfil, o provedor, o modelo e os controles de plugins, hooks e MCP.
3. Repetir a resposta curta com o perfil normal e limites de tempo e turnos, após verificar esses controles.
4. Registrar sucesso ou erro da conta. Se o plano ou a cota não atender, deixar o bloqueio explícito.
5. Manter a ausência de fallback pago e conferir também as rotas auxiliares.

Não assumir elegibilidade de todos os modelos exibidos no seletor.

A sonda offline retornou o esquema de `clarify`, sem chamada ao modelo. O controle Python bloqueou uma tentativa de criação de socket; isso não constitui isolamento do processo inteiro nem confirmação do comportamento da sessão normal.

A tentativa curta executada usou um comando inadequado orientado pelo assistente: `HERMES_SAFE_MODE=1` desviava do carregamento do perfil, e a sessão foi recusada localmente com `invalid_params` por falta de modelo explícito nesse caminho. A leitura do commit explica a recusa anterior à chamada ao modelo. Não atribuir esse resultado à assinatura ou ao operador. A nova chamada ainda está pendente.

## H2: transferir o método

A habilidade privada tem nove modos de análise. Eles serão inicialmente percursos de um coordenador, com estas frentes propostas:

| Frente | Modos da habilidade de origem |
| --- | --- |
| Produto e Validação | diagnóstico e validar |
| Estratégia e Mercado | estratégia e mercado |
| Finanças e Captação | financeiro, captação e pitch |
| Tecnologia e Evidências | tecnologia |
| Execução | execução |

Os nomes com barra da habilidade não são comandos nativos do Hermes já implementados.

Conferir os 44 arquivos e os links relativos antes da importação. A cópia de preparação foi verificada fora do PC; a chegada ao WSL ainda precisa ser comprovada. A base e seu pacote de transferência permanecem privados.

Testar caixa, LTV, demanda paga, projeção versus resultado realizado, custo de IA por entrega aceita e citações. Usar somente dados fictícios até passar pelos controles necessários.

## H3: proteger e recuperar

Perfis, prompts, permissões de leitura do mesmo dono e backend Docker apenas do terminal não constituem isolamento do processo inteiro.

Antes de ferramentas operacionais ou conteúdo externo não confiável:

- Definir os arquivos, destinos e ações permitidos.
- Isolar o processo inteiro e conferir os acessos por testes negativos.
- Proteger a base revisada contra escrita sem impedir o runtime de gravar seu próprio estado.
- Testar que a falta de um canal de aprovação resulta em recusa.
- Fazer backup privado e restaurar em outro diretório.
- Evitar mistura de dados entre casos e projetos.

## H4: criar o agente no Radar

Confirmar a versão instalada do Paperclip e a presença de `hermes_local`. Não substituir esse adaptador por um gateway da VPS por conveniência.

Parâmetros iniciais propostos: um coordenador, concorrência 1, até 8 turnos e 300 segundos por execução, sem rotinas automáticas. Conferir os campos e as ferramentas efetivas da versão instalada. Na documentação atual do adaptador, ferramenta não especificada pode resultar em `all`.

O aceite exige uma tarefa nova com resposta registrada e disposição final correta. Um teste de autenticação, sozinho, não encerra este marco.

## H5: ampliar com evidência

Avaliar novos processos apenas quando houver trabalho recorrente e divisão clara de responsabilidades. Cada processo deve ter estado próprio; clientes distintos podem exigir separação adicional no sistema operacional.

GitHub, n8n, canais de mensagens, VPS e Cloudflare dependem de escopo e testes próprios. Nenhuma dessas integrações foi criada por esta implantação local.

## Como evoluir o repositório

Uma mudança coerente por branch, evidências com dados fictícios, PR e revisão antes de merge. Registrar versões e limitações. Manter credenciais, códigos de login, acervo privado e backups fora do GitHub.

[Instalação e estado atual](docs/hermes-startups-radar.md) · [Histórico](CHANGELOG.md) · [Início](README.md)
