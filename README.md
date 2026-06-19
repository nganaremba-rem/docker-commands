# The Complete Docker Guide & Command Reference

> A from-scratch guide to Docker that explains what it is (in plain English), then teaches **every** command available on your machine using simple **wh-questions** (*What? Why? When? How?*) plus a runnable example.
>
> Generated against **Docker version 29.5.2** — the exact version installed on this machine, so every command listed here really exists for you.

---

## Table of Contents

1. [What is Docker?](#1-what-is-docker)
2. [Docker in layman's terms](#2-docker-in-laymans-terms)
3. [How to read the command reference (two command styles)](#3-how-to-read-the-command-reference-two-command-styles)
4. [Common / top-level commands](#4-common--top-level-commands)
5. [Container lifecycle & inspection commands](#5-container-lifecycle--inspection-commands)
6. [Image commands](#6-image-commands)
7. [Network commands](#7-network-commands)
8. [Volume / data persistence commands](#8-volume--data-persistence-commands)
9. [System / housekeeping commands](#9-system--housekeeping-commands)
10. [Docker Compose](#10-docker-compose)
11. [Build tooling — Builder / Buildx](#11-build-tooling--builder--buildx)
12. [Swarm / orchestration](#12-swarm--orchestration)
13. [Other management groups (context, plugin, manifest, scout, ai)](#13-other-management-groups)
14. [Global options](#14-global-options)
15. [Cheat-sheet & typical workflows](#15-cheat-sheet--typical-workflows)

---

## 1. What is Docker?

**Docker is a platform for building, shipping, and running applications inside *containers*.**

A **container** is a lightweight, isolated package that bundles an application together with *everything it needs to run* — the code, the runtime (e.g. Python, Node.js), the system libraries, and the settings. Because the package carries its own environment, it runs the same way on your laptop, on a colleague's machine, and on a server in the cloud.

### The core vocabulary (learn these six words and the rest is easy)

| Term | Plain meaning |
|------|---------------|
| **Image** | The **blueprint / recipe**. A read-only template that contains your app and its environment. You build it once and reuse it. |
| **Container** | A **running (or stoppable) instance of an image**. You can start many containers from one image. |
| **Dockerfile** | A plain text **recipe file** with step-by-step instructions Docker follows to build an image. |
| **Registry / Docker Hub** | The **app store for images**. A place to download (`pull`) and upload (`push`) images. Docker Hub is the default public one. |
| **Volume** | **Persistent storage** that lives outside the container, so your data survives even when the container is deleted. |
| **Network** | The **virtual wiring** that lets containers talk to each other and to the outside world. |

### The mental model

```
Dockerfile  --(docker build)-->  Image  --(docker run)-->  Container (running app)
   recipe                       blueprint                    the live thing
```

You write a **Dockerfile**, you `build` it into an **Image**, and you `run` the image to get a live **Container**. Images can be shared through a **Registry**.

---

## 2. Docker in layman's terms

### Analogy 1 — Shipping containers 🚢

Before standardized shipping containers, loading a ship was chaos: every item had a different shape and had to be handled differently. The steel shipping container fixed this — one standard box that any crane, truck, ship, or port can handle, no matter what's inside.

Software used to have the same chaos: every app needed a different setup to run. **Docker is the standardized shipping container for software.** You put your app in a Docker container, and any machine that runs Docker can run it — identically — without caring what's inside.

### Analogy 2 — Recipe and cake 🎂

- An **image** is a **recipe** (or a cake mold). It describes exactly how to make the thing.
- A **container** is the **actual cake** you bake from that recipe.
- From **one** recipe you can bake **many identical cakes** (run many containers from one image).
- **Docker Hub** is the giant **cookbook library** where people share recipes.

### "Why not just install software normally?"

You've probably heard the phrase **"but it works on my machine!"** — a program runs fine for one person and breaks for another because their computers have different versions of things installed. Docker eliminates this: the container carries its own correct environment with it, so "my machine" and "your machine" become the same machine *inside the box*.

### Container vs. Virtual Machine (one paragraph)

A **virtual machine (VM)** is like running a whole second computer (its own full operating system) on top of yours — powerful but heavy and slow to start. A **container** is lighter: it **shares the host computer's operating-system kernel** and only packages the app and its dependencies. Result: containers start in seconds, use far less memory, and you can run many more of them on the same hardware.

---

## 3. How to read the command reference (two command styles)

Modern Docker gives you **two ways to write most commands** that mean the same thing:

| Short / common form | Grouped "management" form |
|---------------------|---------------------------|
| `docker run …`      | `docker container run …`  |
| `docker ps`         | `docker container ls`     |
| `docker rmi …`      | `docker image rm …`       |
| `docker images`     | `docker image ls`         |

The **short forms** are quicker and what you'll type daily. The **management forms** (`docker container …`, `docker image …`, `docker network …`, etc.) are more organized and discoverable (`docker container --help` shows everything about containers). This guide uses the common form and points out the management equivalent where helpful.

Every command also supports `--help`:

```bash
docker run --help        # full list of flags for `run`
docker container --help  # everything you can do with containers
```

The wh-format used below for each command:

- **What** — what it does, in one line.
- **Why** — the problem it solves.
- **When** — the situation you reach for it.
- **How** — the syntax skeleton + the flags worth knowing.
- **Example** — a real command and what it does.

---

## 4. Common / top-level commands

These are the everyday commands Docker itself lists first under "Common Commands."

### `docker run`
- **What** — Create **and** start a new container from an image.
- **Why** — It's the single most-used command: it turns a static image into a live, running container.
- **When** — Whenever you want to actually *run* something — a web server, a database, a one-off script.
- **How** — `docker run [OPTIONS] IMAGE [COMMAND]`
  - `-d` run in background (detached); `-it` interactive terminal; `--name` give it a name; `-p host:container` publish a port; `-e KEY=val` set env var; `-v vol:/path` mount a volume; `--rm` auto-delete when it exits.
- **Example**
  ```bash
  docker run -d --name web -p 8080:80 nginx
  # Downloads nginx if needed, runs it in the background, and maps
  # http://localhost:8080 on your machine to port 80 inside the container.
  ```

### `docker exec`
- **What** — Run a command **inside an already-running container**.
- **Why** — To inspect, debug, or operate a live container without restarting it.
- **When** — "I need a shell inside that running container" or "run this one command in there."
- **How** — `docker exec [OPTIONS] CONTAINER COMMAND`  — `-it` for an interactive shell.
- **Example**
  ```bash
  docker exec -it web bash
  # Opens an interactive bash shell inside the running container named "web".
  ```

### `docker ps`  *(= `docker container ls`)*
- **What** — List containers.
- **Why** — To see what's currently running, with their IDs, names, ports, and status.
- **When** — Constantly — to find a container's name/ID before acting on it.
- **How** — `docker ps [OPTIONS]` — `-a` show all (including stopped); `-q` show only IDs; `-s` show sizes.
- **Example**
  ```bash
  docker ps -a
  # Lists every container, running or stopped.
  ```

### `docker build`
- **What** — Build an image from a `Dockerfile`.
- **Why** — To turn your recipe into a reusable image.
- **When** — Whenever you've written or changed a Dockerfile.
- **How** — `docker build [OPTIONS] PATH` — `-t name:tag` name/tag it; `-f Dockerfile.dev` use a specific file; `.` = build context (current dir).
- **Example**
  ```bash
  docker build -t myapp:1.0 .
  # Reads the Dockerfile in the current folder and builds an image tagged myapp:1.0.
  ```

### `docker bake`  *(buildx)*
- **What** — Build one or many images from a configuration file (`docker-bake.hcl`/JSON/Compose) instead of long command lines.
- **Why** — To define complex/repeatable builds (multiple targets, platforms, args) declaratively.
- **When** — You have several images or build variants and want one command to build them all.
- **How** — `docker bake [OPTIONS] [TARGET...]`
- **Example**
  ```bash
  docker bake
  # Builds all targets defined in docker-bake.hcl in the current directory.
  ```

### `docker pull`
- **What** — Download an image from a registry to your machine.
- **Why** — You need an image before you can run it (though `run` will auto-pull if missing).
- **When** — Pre-fetching images, or updating to a newer version.
- **How** — `docker pull NAME[:TAG]` — default tag is `latest`.
- **Example**
  ```bash
  docker pull postgres:16
  # Downloads the PostgreSQL 16 image from Docker Hub.
  ```

### `docker push`
- **What** — Upload your image to a registry so others (or servers) can use it.
- **Why** — To share/deploy images.
- **When** — After building and tagging an image you want to publish.
- **How** — `docker push NAME[:TAG]` (you must be logged in and the name must match your repo).
- **Example**
  ```bash
  docker push myuser/myapp:1.0
  # Uploads myuser/myapp:1.0 to Docker Hub.
  ```

### `docker images`  *(= `docker image ls`)*
- **What** — List the images stored locally.
- **Why** — To see what you've downloaded/built and how much space they use.
- **When** — Cleaning up, checking versions/tags.
- **How** — `docker images [OPTIONS]` — `-a` include intermediate; `-q` only IDs.
- **Example**
  ```bash
  docker images
  # Shows REPOSITORY, TAG, IMAGE ID, CREATED, SIZE for every local image.
  ```

### `docker login` / `docker logout`
- **What** — Authenticate to (or sign out of) a registry.
- **Why** — Pushing images and pulling private images requires authentication.
- **When** — Before your first `push`, or when switching accounts/registries.
- **How** — `docker login [SERVER]` (defaults to Docker Hub); `docker logout [SERVER]`.
- **Example**
  ```bash
  docker login                  # prompts for username/password (Docker Hub)
  docker login registry.example.com
  docker logout
  ```

### `docker search`
- **What** — Search Docker Hub for images from the command line.
- **Why** — To discover official/popular images without opening a browser.
- **When** — Looking for an image to base your app on.
- **How** — `docker search TERM` — `--filter is-official=true`, `--limit N`.
- **Example**
  ```bash
  docker search redis
  # Lists Redis images on Docker Hub with star counts and "official" flags.
  ```

### `docker version` / `docker info`
- **What** — `version` shows client/server version details; `info` shows system-wide info (number of containers/images, storage driver, resources).
- **Why** — Troubleshooting and confirming your setup.
- **When** — Checking compatibility or diagnosing the daemon.
- **How** — `docker version`, `docker info`.
- **Example**
  ```bash
  docker version   # e.g. "Docker version 29.5.2, build 79eb04c7d8"
  docker info      # totals, storage driver, kernel, etc.
  ```

---

## 5. Container lifecycle & inspection commands

These all live under `docker container …` too (e.g. `docker container stop`). They manage the life of containers.

### `docker create`
- **What** — Create a container **without starting it**.
- **Why** — To prepare a container (with all its settings) that you start later.
- **When** — You want to set up now and run on a trigger, or pre-create before attaching things.
- **How** — `docker create [OPTIONS] IMAGE` — same options as `run`.
- **Example**
  ```bash
  docker create --name later -p 8080:80 nginx
  # Creates "later" in the "Created" state; start it with `docker start later`.
  ```

### `docker start` / `docker stop` / `docker restart`
- **What** — Start a stopped container, gracefully stop a running one, or stop+start it.
- **Why** — To control a container's run state without recreating it (state/data are kept).
- **When** — Resuming work, applying a restart, freeing resources temporarily.
- **How** — `docker start CONTAINER`; `docker stop CONTAINER` (sends SIGTERM, then SIGKILL after a grace period, `-t SECONDS`); `docker restart CONTAINER`.
- **Example**
  ```bash
  docker stop web
  docker start web
  docker restart web
  ```

### `docker kill`
- **What** — Immediately stop a container by sending a kill signal.
- **Why** — When a graceful `stop` hangs or you need it dead *now*.
- **When** — Unresponsive containers; sending a specific signal.
- **How** — `docker kill [--signal SIG] CONTAINER` (default `SIGKILL`).
- **Example**
  ```bash
  docker kill web              # force-stop immediately
  docker kill --signal HUP web # send SIGHUP instead
  ```

### `docker pause` / `docker unpause`
- **What** — Freeze (suspend) all processes in a container, then resume them.
- **Why** — To temporarily halt a container's CPU activity without stopping it.
- **When** — Briefly freeing CPU, or pausing during maintenance.
- **How** — `docker pause CONTAINER`; `docker unpause CONTAINER`.
- **Example**
  ```bash
  docker pause web
  docker unpause web
  ```

### `docker rm`  *(= `docker container rm`)*
- **What** — Remove (delete) one or more containers.
- **Why** — To clean up stopped containers and reclaim names/space.
- **When** — After a container is no longer needed.
- **How** — `docker rm CONTAINER...` — `-f` force-remove a running one; `-v` also remove anonymous volumes.
- **Example**
  ```bash
  docker rm later
  docker rm -f web          # stop and remove in one step
  ```

### `docker container prune`
- **What** — Remove **all stopped** containers at once.
- **Why** — Bulk cleanup instead of removing them one by one.
- **When** — Tidying up after lots of experiments.
- **How** — `docker container prune` (asks for confirmation; `-f` to skip).
- **Example**
  ```bash
  docker container prune
  # Deletes every stopped container; reports space reclaimed.
  ```

### `docker rename`
- **What** — Change a container's name.
- **Why** — Friendlier names; avoid name clashes.
- **When** — You created something with an auto-generated name and want a clear one.
- **How** — `docker rename OLD NEW`.
- **Example**
  ```bash
  docker rename gracious_turing web
  ```

### `docker attach`
- **What** — Connect your terminal's input/output to a running container's main process.
- **Why** — To watch or interact with a container's foreground process.
- **When** — You ran something detached and want to "look inside" the main process. (Note: detaching cleanly is `Ctrl-p Ctrl-q`; use `exec` for a separate shell.)
- **How** — `docker attach CONTAINER`.
- **Example**
  ```bash
  docker attach web
  ```

### `docker logs`
- **What** — Show the output (stdout/stderr) a container has produced.
- **Why** — The #1 way to debug what an app is doing.
- **When** — Anytime something misbehaves or you want to watch activity.
- **How** — `docker logs [OPTIONS] CONTAINER` — `-f` follow (live tail); `--tail N` last N lines; `-t` timestamps.
- **Example**
  ```bash
  docker logs -f --tail 100 web
  # Shows the last 100 lines and keeps streaming new ones.
  ```

### `docker inspect`
- **What** — Return low-level details about any Docker object (container, image, network, volume) as JSON.
- **Why** — To see exact configuration: IP address, mounts, env vars, state, etc.
- **When** — Deep debugging or scripting against precise fields.
- **How** — `docker inspect OBJECT` — `--format '{{.NetworkSettings.IPAddress}}'` to extract one field.
- **Example**
  ```bash
  docker inspect web
  docker inspect --format '{{.State.Status}}' web   # e.g. "running"
  ```

### `docker stats`
- **What** — Live stream of resource usage (CPU, memory, network, disk I/O) per container.
- **Why** — To monitor performance and spot resource hogs.
- **When** — Watching load in real time.
- **How** — `docker stats [CONTAINER...]` — `--no-stream` for a one-time snapshot.
- **Example**
  ```bash
  docker stats          # live table for all running containers
  docker stats web --no-stream
  ```

### `docker top`
- **What** — Show the running processes inside a container (like `ps` in there).
- **Why** — To see what processes a container is actually running.
- **When** — Verifying a process is alive, checking PIDs.
- **How** — `docker top CONTAINER`.
- **Example**
  ```bash
  docker top web
  ```

### `docker port`
- **What** — Show the port mappings for a container.
- **Why** — To confirm which host port reaches which container port.
- **When** — "Which localhost port is this service on?"
- **How** — `docker port CONTAINER [PRIVATE_PORT]`.
- **Example**
  ```bash
  docker port web
  # e.g. 80/tcp -> 0.0.0.0:8080
  ```

### `docker diff`
- **What** — Show files/dirs that changed in a container's filesystem since it started (Added/Changed/Deleted).
- **Why** — To see what an app modified at runtime.
- **When** — Auditing changes before `commit`, or debugging.
- **How** — `docker diff CONTAINER`.
- **Example**
  ```bash
  docker diff web
  # A /root/.cache   C /var/log   D /tmp/old
  ```

### `docker wait`
- **What** — Block until a container stops, then print its exit code.
- **Why** — To synchronize scripts on a container finishing.
- **When** — Running a batch job and reacting to its result.
- **How** — `docker wait CONTAINER`.
- **Example**
  ```bash
  docker wait job1
  # ...waits... then prints e.g. 0 (success) or 1 (error).
  ```

### `docker update`
- **What** — Change resource limits/config of existing container(s) without recreating them.
- **Why** — Adjust CPU/memory caps or restart policy on the fly.
- **When** — A container needs more/less resources.
- **How** — `docker update [OPTIONS] CONTAINER` — `--memory`, `--cpus`, `--restart`.
- **Example**
  ```bash
  docker update --memory 512m --cpus 1.5 web
  ```

### `docker cp`
- **What** — Copy files/folders between a container and your host.
- **Why** — Move config in or pull artifacts/logs out.
- **When** — Quick file transfer without rebuilding.
- **How** — `docker cp SRC CONTAINER:DEST` or `docker cp CONTAINER:SRC DEST`.
- **Example**
  ```bash
  docker cp ./index.html web:/usr/share/nginx/html/index.html
  docker cp web:/var/log/nginx/access.log ./access.log
  ```

### `docker commit`
- **What** — Create a new image from a container's current state.
- **Why** — Snapshot changes you made interactively into a reusable image.
- **When** — Quick-and-dirty image creation (prefer a Dockerfile for real work).
- **How** — `docker commit [OPTIONS] CONTAINER [IMAGE[:TAG]]`.
- **Example**
  ```bash
  docker commit web myapp:snapshot
  ```

### `docker export` / `docker import`
- **What** — `export` saves a **container's filesystem** as a flat `.tar`; `import` turns such a tarball back into an **image**.
- **Why** — Move a container's files around as a single archive. (Note: this loses image history/layers — for full images use `save`/`load`, see §6.)
- **When** — Sharing a flattened filesystem snapshot.
- **How** — `docker export CONTAINER -o file.tar`; `docker import file.tar newimage:tag`.
- **Example**
  ```bash
  docker export web -o web-fs.tar
  docker import web-fs.tar web-image:flat
  ```

---

## 6. Image commands

Manage the blueprints. All available under `docker image …`.

### `docker image ls`  *(= `docker images`)*
- **What** — List local images. (See §4 `docker images`.)
- **Example**
  ```bash
  docker image ls
  ```

### `docker image build`  *(= `docker build`)*
- **What** — Build an image from a Dockerfile. (See §4 `docker build`.)
- **Example**
  ```bash
  docker image build -t myapp:1.0 .
  ```

### `docker image pull` / `docker image push`
- **What** — Download / upload images. (See §4 `docker pull`/`docker push`.)
- **Example**
  ```bash
  docker image pull alpine:3.20
  docker image push myuser/myapp:1.0
  ```

### `docker tag`
- **What** — Create a new name/tag that points to an existing image.
- **Why** — To version images and to name them for a specific registry before pushing.
- **When** — Before `push`, or to alias `myapp:1.0` as `myapp:latest`.
- **How** — `docker tag SOURCE[:TAG] TARGET[:TAG]`.
- **Example**
  ```bash
  docker tag myapp:1.0 myuser/myapp:latest
  ```

### `docker rmi`  *(= `docker image rm`)*
- **What** — Remove one or more images.
- **Why** — Free disk space, drop old versions.
- **When** — An image is unused.
- **How** — `docker rmi IMAGE...` — `-f` force.
- **Example**
  ```bash
  docker rmi myapp:snapshot
  ```

### `docker image inspect`
- **What** — Detailed JSON about an image (layers, env, entrypoint, architecture).
- **Why** — Understand exactly what's in/configured on an image.
- **How** — `docker image inspect IMAGE`.
- **Example**
  ```bash
  docker image inspect nginx
  ```

### `docker history`
- **What** — Show the layers that make up an image and how each was created.
- **Why** — See how an image was built and what's inflating its size.
- **When** — Optimizing image size; auditing.
- **How** — `docker history IMAGE`.
- **Example**
  ```bash
  docker history nginx
  ```

### `docker image prune`
- **What** — Remove unused images.
- **Why** — Reclaim space taken by dangling/unreferenced images.
- **When** — Regular cleanup.
- **How** — `docker image prune` (dangling only) — `-a` remove **all** unused; `-f` no prompt.
- **Example**
  ```bash
  docker image prune -a
  ```

### `docker save` / `docker load`
- **What** — `save` writes one or more **images (with full layers/history)** to a tar archive; `load` restores them.
- **Why** — Move complete images between machines **without a registry**.
- **When** — Air-gapped environments, backups, offline transfer.
- **How** — `docker save -o file.tar IMAGE...`; `docker load -i file.tar`.
- **Example**
  ```bash
  docker save -o myapp.tar myapp:1.0
  docker load -i myapp.tar
  ```
  > **save/load vs export/import:** `save`/`load` preserve full image history & multiple tags (work on **images**). `export`/`import` flatten a single **container's** filesystem into one layer. Use save/load to move real images; use export/import to grab a snapshot of a container's files.

### `docker image import` / (`docker image load`)
- **What** — Same operations as above, namespaced under `image`.
- **Example**
  ```bash
  docker image load -i myapp.tar
  ```

---

## 7. Network commands

Containers talk to each other and the world over Docker **networks**. Manage them with `docker network …`.

> **Quick primer on network types:**
> - **bridge** (default) — a private internal network on your host; containers on the same bridge can reach each other; you publish ports (`-p`) to reach them from outside.
> - **host** — the container shares the host's network directly (no isolation, no `-p` needed).
> - **none** — no networking at all (fully isolated).

### `docker network ls`
- **What** — List networks.
- **Why** — See what networks exist and their drivers.
- **How** — `docker network ls`.
- **Example**
  ```bash
  docker network ls
  ```

### `docker network create`
- **What** — Create a new network.
- **Why** — Give a group of containers a private network where they can find each other by name.
- **When** — Multi-container apps that need to communicate.
- **How** — `docker network create [--driver bridge] NAME`.
- **Example**
  ```bash
  docker network create appnet
  docker run -d --name db --network appnet postgres:16
  docker run -d --name api --network appnet myapp:1.0
  # "api" can now reach the database at the hostname "db".
  ```

### `docker network connect` / `docker network disconnect`
- **What** — Attach / detach a running container to/from a network.
- **Why** — Change connectivity without recreating containers.
- **When** — Adding an existing container to another network.
- **How** — `docker network connect NET CONTAINER`; `docker network disconnect NET CONTAINER`.
- **Example**
  ```bash
  docker network connect appnet web
  docker network disconnect appnet web
  ```

### `docker network inspect`
- **What** — Detailed JSON about a network (subnet, gateway, connected containers).
- **Why** — Debug connectivity and IP assignments.
- **How** — `docker network inspect NET`.
- **Example**
  ```bash
  docker network inspect appnet
  ```

### `docker network rm` / `docker network prune`
- **What** — Remove a specific network / remove all unused networks.
- **Why** — Clean up networks no longer in use.
- **How** — `docker network rm NET`; `docker network prune`.
- **Example**
  ```bash
  docker network rm appnet
  docker network prune
  ```

---

## 8. Volume / data persistence commands

By default, everything inside a container is **gone when the container is deleted**. **Volumes** are Docker-managed storage that **persists** independently. Manage them with `docker volume …`.

> **Volume vs. bind mount:** A **volume** (`-v myvol:/data`) is managed by Docker in its own area — best for databases and app data. A **bind mount** (`-v /home/me/code:/app`) maps a folder from your host directly into the container — best for live-editing source code during development. There's also `--mount` (more explicit, verbose syntax) which does the same jobs.

### `docker volume create`
- **What** — Create a named volume.
- **Why** — Reserve persistent storage you can attach to containers.
- **When** — Before running a database/stateful service.
- **How** — `docker volume create NAME`.
- **Example**
  ```bash
  docker volume create pgdata
  docker run -d --name db -v pgdata:/var/lib/postgresql/data postgres:16
  # The database files persist in "pgdata" even if you delete the container.
  ```

### `docker volume ls`
- **What** — List volumes.
- **Why** — See what persistent storage exists.
- **How** — `docker volume ls`.
- **Example**
  ```bash
  docker volume ls
  ```

### `docker volume inspect`
- **What** — Detailed JSON about a volume (its real path on host, driver, labels).
- **Why** — Find where data physically lives; debug.
- **How** — `docker volume inspect NAME`.
- **Example**
  ```bash
  docker volume inspect pgdata
  ```

### `docker volume rm` / `docker volume prune`
- **What** — Remove a specific volume / remove all **unused** volumes.
- **Why** — Reclaim disk space. ⚠️ **This deletes data permanently.**
- **When** — A volume is genuinely no longer needed.
- **How** — `docker volume rm NAME`; `docker volume prune` (`-a` for all unused, not just anonymous).
- **Example**
  ```bash
  docker volume rm pgdata
  docker volume prune
  ```

---

## 9. System / housekeeping commands

Daemon-wide info and cleanup, under `docker system …`.

### `docker system df`
- **What** — Show Docker's disk usage (images, containers, volumes, build cache).
- **Why** — See what's eating disk space and how much is reclaimable.
- **When** — Before a cleanup.
- **How** — `docker system df` — `-v` for a detailed per-object breakdown.
- **Example**
  ```bash
  docker system df
  ```

### `docker system info`  *(= `docker info`)*
- **What** — System-wide information about the Docker installation.
- **Example**
  ```bash
  docker system info
  ```

### `docker system events`  *(= `docker events`)*
- **What** — Stream real-time events from the daemon (container start/stop/die, image pull, etc.).
- **Why** — Live monitoring and auditing of what Docker is doing.
- **When** — Debugging "why did this container restart?" or building automation.
- **How** — `docker system events [--filter ...]`.
- **Example**
  ```bash
  docker system events
  # In another terminal, `docker run ...` and watch the events appear.
  ```

### `docker system prune`
- **What** — Remove unused data: stopped containers, unused networks, dangling images, build cache.
- **Why** — The big one-shot cleanup to reclaim disk.
- **When** — Disk getting full; periodic tidy-up.
- **How** — `docker system prune` — add `-a` to also remove all unused images, `--volumes` to include volumes, `-f` to skip the prompt.
- **Example**
  ```bash
  docker system prune -a
  # Frees the most space (be sure you don't need those images!).
  ```

---

## 10. Docker Compose

**What is Compose?** Real apps usually need several containers (web + database + cache). Typing many `docker run` commands and wiring networks by hand is tedious. **Docker Compose lets you describe your whole multi-container app in one YAML file** (`compose.yaml`) and manage it all with single commands. Run with `docker compose …`.

A tiny `compose.yaml` so the examples make sense:

```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

### `docker compose up`
- **What** — Create and start everything defined in the compose file.
- **Why** — One command to launch your entire app (containers, networks, volumes).
- **When** — Starting your project for development or running it.
- **How** — `docker compose up` — `-d` detached; `--build` rebuild images first.
- **Example**
  ```bash
  docker compose up -d
  # Starts web + db in the background and wires them on a shared network.
  ```

### `docker compose down`
- **What** — Stop and remove the containers and networks Compose created.
- **Why** — Cleanly tear down the whole app.
- **When** — Done working; resetting.
- **How** — `docker compose down` — `-v` also remove named volumes; `--rmi all` remove images too.
- **Example**
  ```bash
  docker compose down
  docker compose down -v   # also wipe the pgdata volume
  ```

### `docker compose ps` / `docker compose ls`
- **What** — `ps` lists containers in the current project; `ls` lists all running Compose projects.
- **Example**
  ```bash
  docker compose ps
  docker compose ls
  ```

### `docker compose logs`
- **What** — View output from the services.
- **How** — `docker compose logs [-f] [SERVICE]`.
- **Example**
  ```bash
  docker compose logs -f web
  ```

### `docker compose build`
- **What** — Build (or rebuild) images for services that have a `build:` section.
- **Example**
  ```bash
  docker compose build
  ```

### `docker compose exec` / `docker compose run`
- **What** — `exec` runs a command in an **already-running** service container; `run` starts a **new one-off** container for a service.
- **When** — `exec` to poke a live service; `run` for one-off tasks (migrations, a shell) before/without the full stack.
- **Example**
  ```bash
  docker compose exec db psql -U postgres
  docker compose run --rm web sh
  ```

### `docker compose start` / `stop` / `restart`
- **What** — Start/stop/restart existing service containers without removing them.
- **Example**
  ```bash
  docker compose stop
  docker compose start
  docker compose restart web
  ```

### `docker compose pull` / `push`
- **What** — Pull all service images / push built service images.
- **Example**
  ```bash
  docker compose pull
  docker compose push
  ```

### `docker compose config`
- **What** — Parse, resolve, and print the final merged compose configuration.
- **Why** — Validate the file and see how variables/overrides resolve.
- **Example**
  ```bash
  docker compose config
  ```

### `docker compose create` / `rm` / `kill`
- **What** — `create` makes service containers without starting them; `rm` removes stopped ones; `kill` force-stops them.
- **Example**
  ```bash
  docker compose create
  docker compose kill
  docker compose rm
  ```

### `docker compose pause` / `unpause`
- **What** — Freeze / resume service containers.
- **Example**
  ```bash
  docker compose pause
  docker compose unpause
  ```

### `docker compose scale`
- **What** — Run multiple replicas of a service.
- **Why** — Quick horizontal scaling for testing/load.
- **Example**
  ```bash
  docker compose up -d --scale web=3   # or: docker compose scale web=3
  ```

### `docker compose top` / `stats`
- **What** — Show processes / live resource usage for the project's containers.
- **Example**
  ```bash
  docker compose top
  docker compose stats
  ```

### `docker compose cp`
- **What** — Copy files between host and a service container.
- **Example**
  ```bash
  docker compose cp ./seed.sql db:/seed.sql
  ```

### `docker compose watch`
- **What** — Watch your source files and automatically rebuild/refresh containers on change.
- **Why** — Fast inner dev loop without manual rebuilds.
- **Example**
  ```bash
  docker compose watch
  ```

### `docker compose wait`
- **What** — Block until the project's containers stop, then return their exit codes.
- **Example**
  ```bash
  docker compose wait
  ```

### `docker compose events` / `port` / `images` / `version`
- **What** — Stream events / show a service's published port / list images used / show Compose version.
- **Example**
  ```bash
  docker compose events
  docker compose port web 80
  docker compose images
  docker compose version
  ```

### `docker compose publish` / `docker compose bridge`
- **What** — `publish` packages and publishes a Compose application to a registry (OCI artifact); `bridge` converts compose files into other models (e.g. Kubernetes manifests).
- **When** — Distributing a Compose app, or migrating toward Kubernetes.
- **Example**
  ```bash
  docker compose publish myuser/myapp-compose:1.0
  docker compose bridge convert
  ```

---

## 11. Build tooling — Builder / Buildx

Modern Docker builds use **BuildKit** (fast, cache-smart, parallel). **Buildx** is the extended build CLI that adds multi-platform builds and named builders. The `docker builder …` group manages build state/cache; `docker buildx …` drives advanced builds.

### `docker builder build`  *(= `docker build`)*
- **What** — Build an image (the BuildKit-powered build).
- **Example**
  ```bash
  docker builder build -t myapp:1.0 .
  ```

### `docker buildx build`
- **What** — Advanced build supporting multiple platforms and exporters.
- **Why** — Build one image that runs on both Intel/AMD (amd64) and ARM (arm64) — e.g. for Apple Silicon and servers.
- **When** — Publishing images that must run on different CPU architectures.
- **How** — `docker buildx build --platform linux/amd64,linux/arm64 -t myuser/myapp:1.0 --push .`
- **Example**
  ```bash
  docker buildx build --platform linux/amd64,linux/arm64 -t myuser/myapp:1.0 --push .
  ```

### `docker bake`  *(= `docker buildx bake`)*
- **What** — Build from a config file (see §4 `docker bake`).
- **Example**
  ```bash
  docker buildx bake
  ```

### `docker buildx ls` / `create` / `use` / `inspect` / `rm` / `stop`
- **What** — Manage **builder instances** (the engines that perform builds).
- **Why** — Set up builders capable of multi-platform builds, or isolate build environments.
- **When** — First-time multi-arch setup; switching builders.
- **How** —
  ```bash
  docker buildx ls                          # list builders
  docker buildx create --name multi --use   # create one and select it
  docker buildx inspect --bootstrap         # show/start the current builder
  docker buildx stop multi
  docker buildx rm multi
  ```

### `docker builder du` / `docker builder prune`
- **What** — Show build-cache disk usage / remove build cache.
- **Why** — Build cache speeds rebuilds but grows large; prune to reclaim space.
- **Example**
  ```bash
  docker builder du
  docker builder prune
  ```

### `docker buildx dial-stdio`
- **What** — Proxy stdio to a builder (low-level; used by tooling/integrations).
- **When** — Rarely by hand — mostly internal.
- **Example**
  ```bash
  docker buildx dial-stdio
  ```

### Buildx sub-management: `history`, `imagetools`, `policy`
- **What** —
  - `docker buildx history` — work with build records (inspect past builds).
  - `docker buildx imagetools` — inspect/create image manifests in a registry (great for multi-arch).
  - `docker buildx policy` — manage build policies.
- **Example**
  ```bash
  docker buildx imagetools inspect myuser/myapp:1.0
  # Shows the platforms and digests in a (multi-arch) image without pulling it.
  ```

---

## 12. Swarm / orchestration

**What is orchestration?** Running containers on one laptop is easy; running them reliably across **many machines** (restarting on failure, scaling, rolling updates, load balancing) needs an **orchestrator**. **Docker Swarm** is Docker's built-in clustering/orchestration mode that turns a group of machines into one virtual Docker host. (Kubernetes is the larger industry alternative.)

### `docker swarm init`
- **What** — Turn the current machine into a Swarm **manager** (create a new cluster).
- **Why** — Bootstrap a swarm so you can deploy services across nodes.
- **When** — Setting up the first node of a cluster.
- **How** — `docker swarm init [--advertise-addr IP]`. It prints a `docker swarm join` token for adding more machines.
- **Example**
  ```bash
  docker swarm init
  # Output includes a `docker swarm join --token ...` command for workers.
  ```

### `docker swarm join`
- **What** — Add the current machine to an existing swarm (as worker or manager).
- **Why** — Grow the cluster.
- **When** — On each additional machine.
- **How** — `docker swarm join --token TOKEN MANAGER_IP:2377`.
- **Example**
  ```bash
  docker swarm join --token SWMTKN-1-xxxx 192.168.1.10:2377
  ```

### The broader swarm family (available once a swarm is active)
Once `docker swarm init` has run, these additional command groups become meaningful:
- **`docker node`** — manage the machines (nodes) in the swarm (`docker node ls`).
- **`docker service`** — deploy and scale long-running services across the cluster (`docker service create --replicas 3 nginx`).
- **`docker stack`** — deploy a whole multi-service app (a Compose file) to the swarm (`docker stack deploy -c compose.yaml mystack`).
- **`docker secret`** / **`docker config`** — securely distribute passwords/keys and config files to services.

> These don't appear in `docker --help` until relevant, but they're the heart of using Swarm in production.

---

## 13. Other management groups

Less common but real command groups on your install.

### `docker context`
- **What** — Manage **contexts** — saved connections to different Docker engines (local, remote SSH, cloud).
- **Why** — Switch which Docker daemon your CLI talks to without retyping connection details.
- **When** — Managing remote servers or multiple environments.
- **How** —
  ```bash
  docker context ls
  docker context create remote --docker "host=ssh://user@server"
  docker context use remote     # now commands run against the remote engine
  docker context use default
  ```

### `docker plugin`
- **What** — Install and manage Docker **plugins** (extra volume/network/log drivers).
- **Why** — Extend Docker with third-party storage/network capabilities.
- **When** — You need a special driver (e.g. a cloud storage volume plugin).
- **How** —
  ```bash
  docker plugin ls
  docker plugin install vieux/sshfs
  docker plugin rm vieux/sshfs
  ```

### `docker manifest`
- **What** — Inspect and create **manifest lists** (multi-architecture image references).
- **Why** — Combine per-arch images under one tag so `docker pull` auto-selects the right CPU build. (Buildx often does this for you.)
- **When** — Manually assembling multi-arch images.
- **How** —
  ```bash
  docker manifest inspect nginx
  docker manifest create myuser/app:1.0 myuser/app:amd64 myuser/app:arm64
  docker manifest push myuser/app:1.0
  ```

### `docker scout`  *(plugin)*
- **What** — **Security scanning** — analyze images for known vulnerabilities (CVEs) and suggest fixes.
- **Why** — Catch insecure dependencies/base images before shipping.
- **When** — In CI or before pushing an image to production.
- **How** —
  ```bash
  docker scout quickview nginx     # summary of vulnerabilities
  docker scout cves nginx          # detailed CVE list
  ```

### `docker ai`  *("Ask Gordon", plugin)*
- **What** — Docker's built-in **AI agent** that answers Docker questions and can help with your containers/Dockerfiles in natural language.
- **Why** — Get help and run Docker-aware AI assistance from the terminal.
- **When** — You want guidance or automation without leaving the CLI.
- **How** —
  ```bash
  docker ai "how do I reduce my image size?"
  ```

---

## 14. Global options

Flags that work on the `docker` command itself (placed before the subcommand). From `docker --help`:

| Option | What it does |
|--------|--------------|
| `-c, --context string` | Use a specific context (which daemon to talk to). Overrides `DOCKER_HOST`. |
| `-H, --host string` | Daemon socket to connect to (e.g. `ssh://…`, `tcp://…`). |
| `-D, --debug` | Enable debug mode (verbose client output). |
| `-l, --log-level string` | Logging level: `debug`, `info`, `warn`, `error`, `fatal`. |
| `--config string` | Location of client config files (default `~/.docker`). |
| `--tls`, `--tlsverify`, `--tlscacert`, `--tlscert`, `--tlskey` | Secure the connection to a remote daemon with TLS. |
| `-v, --version` | Print the Docker version and exit. |

**Example**
```bash
docker -D info                      # run `info` with debug output
docker -H ssh://user@server ps      # list containers on a remote host
docker --context remote ps          # same idea, via a saved context
```

---

## 15. Cheat-sheet & typical workflows

### Most-used commands at a glance

| Goal | Command |
|------|---------|
| Run a container | `docker run -d -p 8080:80 --name web nginx` |
| List running containers | `docker ps` |
| List all containers | `docker ps -a` |
| Open a shell in a container | `docker exec -it web bash` |
| View logs (live) | `docker logs -f web` |
| Stop / start / restart | `docker stop web` / `docker start web` / `docker restart web` |
| Remove a container | `docker rm -f web` |
| List images | `docker images` |
| Build an image | `docker build -t myapp:1.0 .` |
| Tag an image | `docker tag myapp:1.0 myuser/myapp:1.0` |
| Push / pull | `docker push myuser/myapp:1.0` / `docker pull nginx` |
| Remove an image | `docker rmi myapp:1.0` |
| Start a whole app | `docker compose up -d` |
| Tear it down | `docker compose down` |
| Free disk space | `docker system prune -a` |
| See disk usage | `docker system df` |

### Workflow A — run an existing image

```bash
docker pull nginx            # 1. get the image (optional; run auto-pulls)
docker run -d -p 8080:80 --name web nginx   # 2. run it
docker ps                    # 3. confirm it's up
docker exec -it web bash     # 4. poke inside if needed
docker logs -f web           # 5. watch its output
docker stop web              # 6. stop when done
docker rm web                # 7. clean up
```

### Workflow B — build and publish your own image

```bash
# 1. write a Dockerfile, then:
docker build -t myapp:1.0 .          # build the image
docker run --rm -p 3000:3000 myapp:1.0   # test it locally
docker tag myapp:1.0 myuser/myapp:1.0    # name it for your registry
docker login                         # authenticate
docker push myuser/myapp:1.0         # publish it
```

### Workflow C — multi-container app with Compose

```bash
# with a compose.yaml in the folder:
docker compose up -d         # start everything
docker compose ps            # see the services
docker compose logs -f       # watch all logs
docker compose exec db psql -U postgres   # operate a service
docker compose down -v       # stop and remove (incl. volumes)
```

---

### Where to go next

- Learn to write a **Dockerfile** (the recipe that `docker build` consumes) — that's the next foundational skill after these commands.
- Get full help for any command at any time: `docker COMMAND --help` (e.g. `docker run --help`).
- Official docs: <https://docs.docker.com>

*Reference generated for Docker 29.5.2. Run `docker version` to confirm yours matches.*
