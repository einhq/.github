# EiN — Everything is a Node

EiN is a system you host yourself, built on one idea: files, accounts, logs, queues and even its own
commands are nodes in one tree, each with an owner and permissions. You work on the tree from a web
interface or a real bash shell, pipelines act on every change, and modules decide what it is for.
The Archive module makes it a family archive: people and their family tree, photos and videos,
places, and an assistant that answers questions about them. More at
[einhq.github.io](https://einhq.github.io).

**EiN is work in progress.** It runs, but nothing is released yet: there is no installer and no
published image, so it is built from source.

## Running it

On one Linux machine with git, rootless podman and [Deno](https://deno.com); an NVIDIA card is optional, everything works without
one, more slowly.

1. **The database**, PostgreSQL with TimescaleDB, pgvector and PostGIS:

   ```bash
   git clone https://github.com/einhq/db && POSTGRES_PASSWORD=<a password> bash db/run.sh
   ```

2. **Folders** for your originals, the renditions, scratch space, and an empty one for older
   originals, which both containers below mount:

   ```bash
   mkdir -p /srv/ein/{files,files-volatile,files-tmp,old}
   ```

3. **The media service**, every media tool and model (an image of about 30 GB):

   ```bash
   git clone https://github.com/einhq/ems && cd ems
   make build
   make run EMS_KEY=<a random key> GPU=1 FILES_PATH=/srv/ein/files OLD_PATH=/srv/ein/old \
     VOLATILE_PATH=/srv/ein/files-volatile TMP_PATH=/srv/ein/files-tmp
   ```

   Leave out `GPU=1` without a card; a card first needs the CDI setup in ems's README.

4. **The assistant's model**, optionally: `git clone https://github.com/einhq/llm && bash llm/run.sh`,
   or a hosted provider, or none.

5. **EiN**, with the modules it runs checked out under `modules/`:

   ```bash
   git clone https://github.com/einhq/core ein && cd ein
   git clone https://github.com/einhq/module-archive modules/archive
   git clone https://github.com/einhq/module-ems modules/ems
   git clone https://github.com/einhq/module-assistant modules/assistant
   printf '%s\n' CFG_EMS_URL=http://127.0.0.1:8181 'CFG_EMS_KEY=<the key from step 3>' > ein.env
   deno task bundle-ui && make container-build
   make container-run ENV_FILE=ein.env POSTGRES_URL=postgres://ein:<the password>@localhost:5432/ein \
     FILE_ROOT_PATH=/srv/ein/files VOLATILE_FILE_ROOT_PATH=/srv/ein/files-volatile \
     FILE_TMP_PATH=/srv/ein/files-tmp OLD_PATH=/srv/ein/old
   ```

6. **Sign in** at port 8080 as `admin`, with the password `podman logs ein` printed
   (`Admin password set to: …`), and change it.

Port 8080 is the web interface and 2222 SSH and SFTP. Put an HTTPS proxy in front of EiN, and keep
the database and the media service behind a firewall.

## The repositories

| Repository                                                                                                                                          | What it is                                                         |
| --------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| [core](https://github.com/einhq/core)                                                                                                               | EiN itself: the node tree, the shell, pipelines, the web interface |
| [module-archive](https://github.com/einhq/module-archive)                                                                                           | the family archive: people, media, places, staging                 |
| [module-assistant](https://github.com/einhq/module-assistant)                                                                                       | the chat that answers questions about the archive                  |
| [module-ems](https://github.com/einhq/module-ems)                                                                                                   | EiN's client for the media service                                 |
| [ems](https://github.com/einhq/ems)                                                                                                                 | the media service: every media tool and model behind one HTTP API  |
| [db](https://github.com/einhq/db)                                                                                                                   | the PostgreSQL image and its run script                            |
| [llm](https://github.com/einhq/llm)                                                                                                                 | the assistant's local language model                               |
| [bash-parser](https://github.com/einhq/bash-parser), [bash-executor](https://github.com/einhq/bash-executor), [ssh2](https://github.com/einhq/ssh2) | the libraries EiN's shell and SSH are built on, on JSR             |
