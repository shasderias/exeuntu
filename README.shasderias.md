# exeuntu personal fork

This repository is a personal fork of exeuntu. It adds `Dockerfile.mini`, a lightweight image variant, and keeps everything else identical to upstream.

`AGENTS.md` is not for repository instructions: the Dockerfiles copy it into every image as the VM's agent instructions. Keep it identical to upstream.

## Repository operations

> **Repository-specific exception:** The rules in this section are dangerous in normal Git workflows. They override any previous Git-operation rules, but only while operating on this repository (exeuntu). They do not apply to any other repository.

- Remotes: `origin` is the personal fork (`shasderias/exeuntu`); `upstream` is `boldsoftware/exeuntu`.
- Keep all personal-fork changes as exactly one commit on top of `upstream/main`. After committing additional work, squash or amend it into that single commit.
- Force-pushing is permissible to the personal fork when required to update the rewritten commit. Never force-push to `upstream`.
- Incorporate upstream changes with `git fetch upstream`, then rebase the local `main` branch onto `upstream/main`. Resolve conflicts while preserving the fork's intentional changes, then restore the one-commit structure.
- After rebasing, carry upstream `Dockerfile` changes into `Dockerfile.mini` as described below.

## exeuntu mini

`Dockerfile.mini` is a smaller exeuntu variant for app-hosting VMs. It stays close to the upstream `Dockerfile` so upstream changes can be adopted without redesigning the image. Use the diff between the files as the source of truth.

Intent: a VM that runs one app, installed and managed through Shelley. Track upstream closely, cut ruthlessly, and keep every exe.dev integration.

Keep:

- Everything exe.dev relies on: the `exe.dev/*` labels, init wrapper, systemd setup, `exedev` user, `exe-setup.service`, Shelley socket/service and tmpfiles, `/exe.dev/etc/gitconfig` include, fstrim timer, growfs fstab, `/tmp` handling, machine-id reset, MOTD, Ghostty terminfo, and the `exeuntu` CLI.
- Shelley instructions: upstream `AGENTS.md` plus `AGENTS.mini.md`, which tells Shelley to offer installing ImageMagick/ffmpeg when its image or video features need them.
- What Shelley needs for its core features, including the browser tool: headless-shell and its libraries and fonts, the eMudhra distrust, and basic tools (`git`, `curl`, `jq`, `ripgrep`, `python3`, `vim-tiny`, `sudo`, `openssh`).

Cut (reinstall on a VM as needed):

- Documentation: no `unminimize`, man pages, or `man-db`. Run `sudo unminimize`.
- Metapackages and extras: `ubuntu-server`, `ubuntu-standard`, `ubuntu-dev-tools`, `build-essential`, `vim` (`vim-tiny` provides `vi`), the GTK/X11/GBM libraries (headless-shell does not use them), `locales-all` (only `en_US.UTF-8` is generated), `mitmproxy`, `neovim`, `nginx`, Docker, `bubblewrap`, `cron`, `logrotate`, monitoring tools, and others. Use `sudo apt-get update && sudo apt-get install ...`; the image ships no apt lists.
- Separately installed tools: `gh`, Tailscale, Go, uv, DuckDB.
- Coding agents: Claude Code, Codex, OpenCode, pi, and their config. pi can be reinstalled, but without upstream's exe.dev pi extension.
- Shelley's optional image/video tools: ImageMagick (HEIC uploads) and ffmpeg (screencasts, video, transcription).

### Maintain

1. Update the repository, then review the variant:

   ```bash
   git diff --no-index Dockerfile Dockerfile.mini
   ```

2. Edit `Dockerfile.mini` and any `.mini` companion files. Preserve upstream structure and make only intentional variant changes.

   If a cut breaks one of Shelley's own features, note it in `AGENTS.mini.md`, which mini appends to Shelley's `AGENTS.md`.

3. Commit the update, then build:

   ```bash
   docker build -f Dockerfile.mini \
     -t 127.0.0.1:8000/public/exeuntu-mini:latest .
   ```

4. Push only when a release is intended:

   ```bash
   docker push 127.0.0.1:8000/public/exeuntu-mini:latest
   ```

   If Docker is not logged in, try:

   ```bash
   docker login 127.0.0.1:8000 -u admin --password-stdin < ~/zot-admin-password
   ```

The public image name is `artifact.exe.xyz/public/exeuntu-mini:latest`.

If information is required about the registry, credentials, namespaces, or access policy, see `~/AGENTS.md`.
