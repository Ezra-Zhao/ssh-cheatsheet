# SSH Cheatsheet · Hoja de referencia de comandos SSH

> 🌍 [中文](README.md) · [Português](README.pt.md)

Una **hoja de referencia de una sola página** con 28 usos reales de SSH: conexión, claves, `~/.ssh/config`, redirección de puertos, servidores de salto, transferencia de archivos y endurecimiento del servidor. Cada entrada responde solo tres preguntas: **qué hace, un comando listo para copiar y pegar, y etiquetas de uso**. Sin parámetros inventados: cada parámetro y opción de configuración está verificado contra las páginas man de OpenSSH 9.6, y los ejemplos de configuración se comprobaron parseándolos con `ssh -G`.

## Para quién es

Ingenieros de operaciones (SRE/DevOps), desarrolladores backend y cualquiera que cada vez que usa `-L` o `-R` tenga que buscar cuál es cuál. Si la dirección de los túneles, la sintaxis del servidor de salto o los errores de claves te frenan, esta hoja te lo deja claro de una vez.

## Cómo usarla

1. Ve directo a **[CHEATSHEET.md](CHEATSHEET.md)**: el cuerpo de la referencia; los comandos en inglés los entiende cualquier desarrollador.
2. Necesitas un cliente OpenSSH: incluido en Linux y macOS; en Windows, el OpenSSH del sistema o WSL. Para practicar basta cualquier servidor en la nube.
3. Sigue el orden de la hoja —conexión → claves → config → redirección de puertos → servidor de salto → transferencia → endurecimiento— y prueba cada comando a mano. En una hora los dominas.

## Pruébalo en 30 segundos

```bash
ssh -V                             # imprime la versión (p. ej. OpenSSH_9.6p1): el cliente está
ssh-keygen -t ed25519              # si aún no tienes clave, genera una (Enter y una frase de paso)
ssh -G example.com | head -5       # sin red: muestra los parámetros efectivos para example.com
```

## Un ~/.ssh/config listo para usar

```sshconfig
Host myserver
    HostName example.com
    User deploy
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
```

Guárdalo en `~/.ssh/config` y `ssh myserver` te conecta de una vez: usuario, host, puerto y clave sin volver a escribirlos.

## Qué incluye

| Categoría | Contenido |
|---|---|
| 🔌 Conexión básica | conexión básica / puerto con -p / clave con -i / depuración con -v / keepalive / escape para desconectar (5) |
| 🔑 Gestión de claves | generar ed25519 con keygen / subir la clave con ssh-copy-id / agente ssh-agent / cambiar la frase de paso / permisos y huella (5) |
| 📝 ~/.ssh/config | plantilla de alias / valores por defecto con Host * / IdentitiesOnly / grupos con comodines (4) |
| 🔀 Redirección de puertos | -L local / -R remota / -D SOCKS / túnel en segundo plano con -fN (4) |
| 🪜 Servidor de salto (ProxyJump) | salto con -J en la terminal / ProxyJump fijo en el config (2) |
| 📤 Transferencia de archivos | subir y bajar con scp / -P y -r de scp / sincronización incremental con rsync / sftp interactivo (4) |
| 🛡️ Endurecimiento (sshd) | PermitRootLogin / solo claves / AllowUsers + MaxAuthTries / cambiar el puerto sin quedarte fuera (4) |

La documentación oficial es solo https://www.openssh.com/; cada comando está verificado contra las páginas man de OpenSSH (ssh / ssh_config / ssh-keygen / scp). Los ejemplos usan example.com y el rango de documentación 192.0.2.x: ningún servidor real.

## Contribuciones bienvenidas

¿Un comando incorrecto, un parámetro desactualizado o una traducción mejorable? Los *pull requests* son bienvenidos. Mantén el formato (descripción / bloque bash / etiquetas) y **solo comandos reales y verificados; ante la duda, mejor no incluirlo**.

## 📚 Más hojas de referencia

Otras hojas del mismo autor, también en 中文 / Español / Português:

- [FFmpeg 命令速查表](https://github.com/Ezra-Zhao/ffmpeg-cheatsheet) — 28 comandos de audio y vídeo
- [Git 实战速查表](https://github.com/Ezra-Zhao/git-cheatsheet) — 30 comandos diarios y de rescate
- [Docker 命令速查表](https://github.com/Ezra-Zhao/docker-cheatsheet) — 28 comandos de contenedores
- [curl 命令速查表](https://github.com/Ezra-Zhao/curl-cheatsheet) — 29 comandos para depurar APIs
- [正则表达式速查表](https://github.com/Ezra-Zhao/regex-cheatsheet) — 30 patrones prácticos de expresiones regulares
- [jq 命令速查表](https://github.com/Ezra-Zhao/jq-cheatsheet) — 30 filtros para JSON
- [终端神器速查表](https://github.com/Ezra-Zhao/terminal-superpowers-cheatsheet) — 14 herramientas de eficiencia para la terminal
- [Adobe 免费替代速查表](https://github.com/Ezra-Zhao/adobe-free-alternatives-cheatsheet) — 16 alternativas gratuitas a Adobe
- [退订省钱速查表](https://github.com/Ezra-Zhao/desubscribe-cheatsheet) — 10 sustituciones de suscripciones, ≈ $1,260/año de ahorro
- [kubectl 命令速查表](https://github.com/Ezra-Zhao/kubectl-cheatsheet) — comandos habituales de Kubernetes
- [SQL 查询速查表](https://github.com/Ezra-Zhao/sql-cheatsheet) — consultas y funciones SQL habituales

## Licencia

MIT © 2026 Guangyi Zhao. Ver [LICENSE](LICENSE).
