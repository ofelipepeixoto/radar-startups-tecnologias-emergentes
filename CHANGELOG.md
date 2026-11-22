# Histórico de construção

## 22/11/2026: runtime local do Hermes preparado

Foi concluída uma instalação assistida do Hermes no Ubuntu/WSL2, usando o commit `d236cbe069577679a2d3b60c42bd58e1fe879ca1` do [repositório Hermes](https://github.com/ofelipepeixoto/hermes-agent/tree/d236cbe069577679a2d3b60c42bd58e1fe879ca1).

As saídas do terminal foram executadas e fornecidas pelo operador. O assistente revisou o código fixado e orientou os comandos; não teve acesso direto ao WSL.

### Preparação e recuperação

- Conferência de caminhos, ferramentas, espaço e instalação existente.
- Backup privado do estado anterior do perfil e dos arquivos de shell pertinentes.
- Instalador baixado para arquivo, com hash e sintaxe conferidos antes da execução.
- A clonagem com histórico falhou com `curl 56`, `early EOF` e interrupção da transferência.
- A recuperação obteve apenas o commit escolhido, por fetch raso com HTTP/1.1 restrito à chamada. A identidade Git foi conferida antes da publicação da pasta.
- Um download posterior de `uv` falhou com `curl 6`, por resolução de nomes. Os diagnósticos seguintes resolveram os dois hosts e a instalação foi retomada. Não foi necessário reiniciar o WSL ou mudar DNS.
- As etapas `venv`, `python-deps`, `config`, `products` e `complete` terminaram com sucesso. O estágio de repositório não foi repetido sobre a cópia rasa.
- O instalador informou a adição de `~/.local/bin` ao PATH em `.bashrc`.

### Conferências posteriores

| Item | Observado |
| --- | --- |
| Identidade do Hermes | `vgit.d236cbe`, upstream `d236cbe0` |
| Python do runtime | 3.14.9 |
| OpenAI SDK | 2.54.0 |
| Adoção automática de logins externos | Desligada |
| Ferramentas selecionadas na configuração | `["clarify"]` |
| Memória e perfil persistente do usuário | Desligados |
| Curator, revisão em segundo plano e geração de título | Desligados |
| Rotas auxiliares | `free_only: true`; revisão completa ainda pendente |
| Provedores de reserva | Lista vazia |
| Concorrência configurada | 1 |
| Autenticação Hermes | `openai-codex: logged in` |
| Autenticação do Codex CLI habitual | `Logged in using ChatGPT` |
| Modelo salvo | `gpt-6.2-sol`, via OpenAI Codex |
| Raciocínio salvo | `medium` |

Os controles foram lidos após a instalação e antes da seleção do modelo. A nova conferência após essa seleção ainda está pendente.

A confirmação do Codex é uma conferência da autenticação armazenada. Não equivale a um novo teste completo do Camaleão no Paperclip.

### Base privada preparada

A árvore da habilidade 0.1.1 foi conferida em ambiente de preparação: 44 Markdown, 189.779 bytes, sem symlinks e com 362 links relativos sem destinos ausentes ou externos à habilidade.

Foi preparado um importador privado que recusa destino existente e alterações de integridade. Oito testes passaram em diretórios temporários desse ambiente. Esses resultados não são testes do Hermes no PC e não comprovam que a base já foi transferida para o WSL.

O conteúdo privado, os pacotes de transferência e seus metadados pessoais não fazem parte deste repositório.

### Limites neste ponto

Ainda não foram concluídos:

- Chamada de modelo pelo Hermes.
- Inspeção das ferramentas efetivamente apresentadas ao modelo.
- Transferência e consulta da base no WSL.
- Aplicação da identidade do coordenador e avaliação das cinco frentes propostas.
- Criação e teste de um agente Hermes no Radar/Paperclip.
- Isolamento do processo inteiro, restauração e testes de separação entre projetos.

Não houve varredura ativa de Garak ou Strix, teste de produção, ativação de API paga ou alteração de VPS/Cloudflare nesta etapa.

## 21/11/2026: guia do plugin 0.1.1

O guia público passou a documentar o plugin privado Radar Startups e Tecnologias Emergentes 0.1.1, seus nove modos e exemplos fictícios.

A conferência da interface do ChatGPT e os exemplos observados ficaram registrados em [acesso](docs/instalacao.md) e [exemplos](docs/exemplos.md). A referência do guia anterior ao trabalho Hermes é `764f9922a8c7cc30915d828a3355fbe8c58d50ff`.

As avaliações históricas do plugin no ChatGPT não validam automaticamente a adaptação ao Hermes.

[Guia Hermes](docs/hermes-startups-radar.md) · [Roadmap](ROADMAP.md) · [Início](README.md)
