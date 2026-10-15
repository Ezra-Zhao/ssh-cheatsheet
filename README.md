# SSH 命令速查表 · SSH Cheatsheet

[![Stars](https://img.shields.io/github/stars/Ezra-Zhao/ssh-cheatsheet?style=social)](https://github.com/Ezra-Zhao/ssh-cheatsheet/stargazers) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE) ![Languages](https://img.shields.io/badge/languages-3-blue)

> 🌍 [Español](README.es.md) · [Português](README.pt.md)

一份**一页纸**的 SSH 速查表：28 条真实可用的 SSH 用法——连接、密钥、`~/.ssh/config`、端口转发、跳板机、文件传输、服务端加固。每个条目只讲三件事——**它是干什么的、一条能直接复制粘贴的命令、适用场景标签**。不编造参数：所有参数与配置项均对照本机 OpenSSH 9.6 的 man 手册逐条核对，config 示例用 `ssh -G` 实际解析验证过。

## 面向谁

运维工程师、后端开发者、SRE，以及任何"每次用 `-L` 还是 `-R` 都要现查一遍"的人。如果隧道方向、跳板机写法、密钥报错这几件事总让你卡壳，这张表能让你一次记牢。

## 怎么用

1. 直接看 **[CHEATSHEET.md](CHEATSHEET.md)** ——速查表正文，英文命令全球开发者都无压力。
2. 本地要有 OpenSSH 客户端：Linux / macOS 自带，Windows 用系统自带 OpenSSH 或 WSL；练习用的服务器可以是任意一台云主机。
3. 按"连接 → 密钥 → config → 端口转发 → 跳板机 → 文件传输 → 加固"的顺序，每个条目亲手敲一遍，一小时就顺手了。

## 先跑起来（30 秒验证环境）

```bash
ssh -V                             # 打印版本（如 OpenSSH_9.6p1），说明客户端在
ssh-keygen -t ed25519              # 还没有密钥的话，先生成一把（一路回车并设个口令）
ssh -G example.com | head -5       # 不联网：打印连接 example.com 时最终生效的参数
```

## 一份能直接用的 ~/.ssh/config

```sshconfig
Host myserver
    HostName example.com
    User deploy
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
```

存进 `~/.ssh/config` 后，`ssh myserver` 一条命令直达——用户、主机、端口、密钥都不用再打。

## 收录了什么

| 分类 | 内容 |
|---|---|
| 🔌 连接基本功 | 基本连接 / -p 端口 / -i 指定密钥 / -v 调试 / 保活 / 转义断开（5 条） |
| 🔑 密钥管理 | keygen 生成 ed25519 / ssh-copy-id 上传公钥 / agent 托管 / 改口令 / 权限与指纹（5 条） |
| 📝 ~/.ssh/config | 别名模板 / Host * 默认设置 / IdentitiesOnly / 通配符分组（4 条） |
| 🔀 端口转发 | -L 本地转发 / -R 远程转发 / -D SOCKS / -fN 后台隧道（4 条） |
| 🪜 跳板机 ProxyJump | -J 命令行跳板 / config 里固定 ProxyJump（2 条） |
| 📤 文件传输 | scp 上传下载 / scp -P 与 -r / rsync 增量同步 / sftp 交互式（4 条） |
| 🛡️ 安全加固（sshd） | PermitRootLogin / 只许密钥登录 / AllowUsers + MaxAuthTries / 改端口与安全改法（4 条） |

官方文档只链 https://www.openssh.com/，所有命令均对照 OpenSSH man 手册（ssh / ssh_config / ssh-keygen / scp）核实过语法；示例中的主机一律用 example.com 与 192.0.2.x 文档地址段，不涉及任何真实服务器。

## 为什么是这 28 条

- **隧道三件套分清**：-L / -R / -D 到底是谁的端口听在谁那边，一句话讲透，不再现查。
- **config 是复利**：别名 + ProxyJump 配置一次，之后每次连接少打一长串，scp / rsync 也自动受益。
- **加固给安全改法**：先验密钥登录、`sshd -t` 校验语法、reload 不断现有会话——照着做，不会把自己锁在门外。

## 欢迎 PR

发现命令写错了、参数过时了、翻译不到位——欢迎提 Pull Request。新增条目请保持统一格式（一句话说明 / bash 代码块 / 标签），**只收真实可用的命令，不确定的宁可不收**。

## 📚 更多速查表

同一作者的其他速查表，同样有中/西/葡三语版本：

- [FFmpeg 命令速查表](https://github.com/Ezra-Zhao/ffmpeg-cheatsheet) — 28 条音视频处理命令
- [Git 实战速查表](https://github.com/Ezra-Zhao/git-cheatsheet) — 30 条日常+救命命令
- [Docker 命令速查表](https://github.com/Ezra-Zhao/docker-cheatsheet) — 28 条容器命令
- [curl 命令速查表](https://github.com/Ezra-Zhao/curl-cheatsheet) — 29 条 API 调试命令
- [正则表达式速查表](https://github.com/Ezra-Zhao/regex-cheatsheet) — 30 条实战正则模式
- [jq 命令速查表](https://github.com/Ezra-Zhao/jq-cheatsheet) — 30 条 JSON 处理 filter
- [终端神器速查表](https://github.com/Ezra-Zhao/terminal-superpowers-cheatsheet) — 14 个终端效率工具
- [Adobe 免费替代速查表](https://github.com/Ezra-Zhao/adobe-free-alternatives-cheatsheet) — 16 个 Adobe 免费替代品
- [退订省钱速查表](https://github.com/Ezra-Zhao/desubscribe-cheatsheet) — 10 组退订替代，年省约 $1,260
- [kubectl 命令速查表](https://github.com/Ezra-Zhao/kubectl-cheatsheet) — Kubernetes 常用命令速查
- [SQL 查询速查表](https://github.com/Ezra-Zhao/sql-cheatsheet) — 常用 SQL 查询与函数速查

## License

MIT © 2026 Guangyi Zhao，详见 [LICENSE](LICENSE)。
