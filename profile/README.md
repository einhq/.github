# EiN — Everything is a Node

EiN is a system you host yourself, built on one idea: files, accounts, logs, queues and even its own
commands are nodes in one tree, each with an owner and permissions. You work on the tree from a web
interface or a real bash shell, pipelines act on every change, and modules decide what it is for.
The Archive module makes it a family archive: people and their family tree, photos and videos,
places, and an assistant that answers questions about them. More at
[einhq.eu](https://einhq.eu).

**EiN is work in progress.** It runs, but nothing is released yet and there are no published
images, so the installer builds it from source.

## Installing it

On one Linux machine with git and rootless podman or Docker, each with its compose. EiN Core, the bare bones installation:

```bash
curl -fsSL https://einhq.eu/install.sh | bash
```

Or EiN Archive, the full Archive installation with the media service and the chat:

```bash
curl -fsSL https://einhq.eu/install.sh | bash -s -- --ems --llm --module archive --module assistant
```

Either checks the machine against the [system requirements](https://einhq.eu/#requirements) and
stops if something is missing, then fetches [deploy](https://github.com/einhq/deploy) into `~/ein`,
builds and starts, and prints the admin password. `--ems` is the media service, which the Archive
needs: an image of about 30 GB, with the NVIDIA card if there is one. `--llm` is the assistant's
model, or a hosted provider set in `~/ein/.env`. The options also go to `~/ein/setup.sh` any time
after, and running it again updates everything. Port 8080 is the web interface and 2222 SSH and
SFTP; put an HTTPS proxy in front of EiN.

## The repositories

| Repository                                                                                                                                          | What it is                                                                        |
| --------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| [core](https://github.com/einhq/core)                                                                                                               | EiN itself: the node tree, the shell, pipelines, the web interface                |
| [module-archive](https://github.com/einhq/module-archive)                                                                                           | the family archive: people, media, places, staging                                |
| [module-assistant](https://github.com/einhq/module-assistant)                                                                                       | the chat that answers questions about the archive                                 |
| [module-ems](https://github.com/einhq/module-ems)                                                                                                   | EiN's client for the media service                                                |
| [ems](https://github.com/einhq/ems)                                                                                                                 | the media service: every media tool and model behind one HTTP API                 |
| [deploy](https://github.com/einhq/deploy)                                                                                                           | EiN on one machine with podman compose or docker compose: what the installer runs |
| [db](https://github.com/einhq/db)                                                                                                                   | the PostgreSQL image and its run script                                           |
| [llm](https://github.com/einhq/llm)                                                                                                                 | the assistant's local language model                                              |
| [bash-parser](https://github.com/einhq/bash-parser), [bash-executor](https://github.com/einhq/bash-executor), [ssh2](https://github.com/einhq/ssh2) | the libraries EiN's shell and SSH are built on, on JSR                            |
