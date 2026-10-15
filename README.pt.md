# SSH Cheatsheet · Cola de referência de comandos SSH

> 🌍 [中文](README.md) · [Español](README.es.md)

Uma **cola de uma página só** com 28 usos reais de SSH: conexão, chaves, `~/.ssh/config`, encaminhamento de portas, servidor de salto, transferência de arquivos e endurecimento do servidor. Cada entrada responde a apenas três perguntas: **o que ela faz, um comando pronto para copiar e colar, e etiquetas de uso**. Sem parâmetros inventados: cada parâmetro e opção de configuração foi conferido nas páginas man do OpenSSH 9.6, e os exemplos de configuração foram validados de verdade com `ssh -G`.

## Para quem é

Engenheiros de operações (SRE/DevOps), desenvolvedores backend e qualquer pessoa que, a cada `-L` ou `-R`, precise pesquisar qual é qual. Se a direção dos túneis, a sintaxe do servidor de salto ou os erros de chave te travam, esta cola resolve isso de uma vez.

## Como usar

1. Vá direto ao **[CHEATSHEET.md](CHEATSHEET.md)**: o corpo da referência; os comandos em inglês qualquer desenvolvedor entende.
2. Você precisa de um cliente OpenSSH: já vem no Linux e no macOS; no Windows, use o OpenSSH do sistema ou o WSL. Para praticar, qualquer servidor na nuvem serve.
3. Siga a ordem da cola —conexão → chaves → config → encaminhamento de portas → servidor de salto → transferência → endurecimento— e teste cada comando na mão. Em uma hora você domina tudo.

## Teste em 30 segundos

```bash
ssh -V                             # imprime a versão (ex.: OpenSSH_9.6p1): o cliente está lá
ssh-keygen -t ed25519              # se ainda não tem chave, gere uma (Enter e uma senha)
ssh -G example.com | head -5       # sem rede: mostra os parâmetros efetivos para example.com
```

## Um ~/.ssh/config pronto para usar

```sshconfig
Host myserver
    HostName example.com
    User deploy
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
```

Salve em `~/.ssh/config` e `ssh myserver` conecta de uma vez: usuário, host, porta e chave sem digitar de novo.

## O que está incluído

| Categoria | Conteúdo |
|---|---|
| 🔌 Conexão básica | conexão básica / porta com -p / chave com -i / depuração com -v / keepalive / escape para desconectar (5) |
| 🔑 Gestão de chaves | gerar ed25519 com keygen / enviar a chave com ssh-copy-id / agente ssh-agent / trocar a senha / permissões e impressão digital (5) |
| 📝 ~/.ssh/config | modelo de alias / padrões com Host * / IdentitiesOnly / grupos com curingas (4) |
| 🔀 Encaminhamento de portas | -L local / -R remoto / -D SOCKS / túnel em segundo plano com -fN (4) |
| 🪜 Servidor de salto (ProxyJump) | salto com -J no terminal / ProxyJump fixo no config (2) |
| 📤 Transferência de arquivos | enviar e baixar com scp / -P e -r do scp / sincronização incremental com rsync / sftp interativo (4) |
| 🛡️ Endurecimento (sshd) | PermitRootLogin / somente chaves / AllowUsers + MaxAuthTries / trocar a porta sem ficar trancado fora (4) |

A documentação oficial é só https://www.openssh.com/; cada comando foi verificado contra as páginas man do OpenSSH (ssh / ssh_config / ssh-keygen / scp). Os exemplos usam example.com e a faixa de documentação 192.0.2.x: nenhum servidor real.

## Contribuições são bem-vindas

Achou um comando errado, um parâmetro desatualizado ou uma tradução a melhorar? *Pull requests* são bem-vindos. Mantenha o formato (descrição / bloco bash / etiquetas) e **só comandos reais e verificados; na dúvida, melhor não incluir**.

## 📚 Mais folhas de referência

Outras colas do mesmo autor, também em 中文 / Español / Português:

- [FFmpeg 命令速查表](https://github.com/Ezra-Zhao/ffmpeg-cheatsheet) — 28 comandos de áudio e vídeo
- [Git 实战速查表](https://github.com/Ezra-Zhao/git-cheatsheet) — 30 comandos do dia a dia e de resgate
- [Docker 命令速查表](https://github.com/Ezra-Zhao/docker-cheatsheet) — 28 comandos de contêineres
- [curl 命令速查表](https://github.com/Ezra-Zhao/curl-cheatsheet) — 29 comandos para depurar APIs
- [正则表达式速查表](https://github.com/Ezra-Zhao/regex-cheatsheet) — 30 padrões práticos de expressões regulares
- [jq 命令速查表](https://github.com/Ezra-Zhao/jq-cheatsheet) — 30 filtros para JSON
- [终端神器速查表](https://github.com/Ezra-Zhao/terminal-superpowers-cheatsheet) — 14 ferramentas de eficiência para o terminal
- [Adobe 免费替代速查表](https://github.com/Ezra-Zhao/adobe-free-alternatives-cheatsheet) — 16 alternativas gratuitas ao Adobe
- [退订省钱速查表](https://github.com/Ezra-Zhao/desubscribe-cheatsheet) — 10 trocas de assinaturas, ≈ $1,260/ano de economia
- [kubectl 命令速查表](https://github.com/Ezra-Zhao/kubectl-cheatsheet) — comandos habituais do Kubernetes
- [SQL 查询速查表](https://github.com/Ezra-Zhao/sql-cheatsheet) — consultas e funções SQL habituais

## Licença

MIT © 2026 Guangyi Zhao. Veja [LICENSE](LICENSE).
