---
marp: true
theme: gaia
size: 16:9
paginate: true
footer: Gitea - 12. Stammtisch der Unix-Freunde Süd-Ost-Oberbayern - July 2026
header: '<img class="corner-logo" src="images/gitea-logo.svg" alt="Gitea logo">'
---
<style>
section > header {
  align-items: center;
  bottom: 0;
  display: flex;
  height: 70px;
  left: 24px;
  line-height: 1;
  padding: 0;
  right: auto;
  top: auto;
}

section > header .corner-logo {
  height: 44px;
  width: auto;
}

section > footer {
  padding-left: 82px;
}
</style>

<!-- _class: lead -->
<!-- paginate: false -->

# Gitea in 15 Minutes
### Run your own collaborative Git service

 Patrick Stein
 
---

# Gitea

- GitHub on your own hardware
- Very simple setup and frontend
- Normal Git functionality (SSH/HTTPS)
- Multi-user support (pull requests, reviews)
- Ticket system (issues, boards)
- Run CI services

<!--
branch protection
-->
---

## Two commands create a durable demo

```sh
# Apple container CLI — tested with Gitea 1.26.4
cd
mkdir gitea-data

container run --name gitea --detach \
  --publish 3000:3000 \
  --publish 2222:22 \
  --volume ${HOME}/gitea-data:/data \
  --env GITEA__server__SSH_PORT=2222 \
  --env GITEA__server__ROOT_URL=http://localhost:3000/ \
  docker.gitea.com/gitea:1.26.4
```
---

## Default setup
- Database: SQLite3
- Site title and server domain
- HTTP port `3000`
- SSH port `2222`
- Create the first administrator
- One directory to protect

---

## Gitea does not replace the Git workflow

```sh
# Add an SSH key in User settings → SSH / GPG Keys first
git clone ssh://git@localhost:2222/tea-lab/hello-gitea.git
cd hello-gitea

git switch -c feature/greeting
printf 'Hello from Gitea\n' > greeting.txt
git add greeting.txt
git commit -m "Add a friendly greeting"
git push -u origin feature/greeting
```
---

## Team features

- Block direct pushes to `main`
- Pull requests
- Require one approval
- Require successful status checks
- Apply rules to administrators

---

# CI/CD features

- Runners
- Queue jobs
- Poll other servers
- Merge requirements
- Build release artifacts
- Supports OCI containers, Swift, npm, Maven, NuGet, PyPI, Cargo, generic files, and many other package formats
---

## Self-hosting responsibilities

- Pin versions and apply security releases promptly.
- Use HTTPS, secure cookies, and a trusted reverse proxy configuration.
- Require 2FA where appropriate; use scoped tokens for automation.
- Protect default branches and restrict force pushes.
- Isolate Actions runners and treat pull-request code as untrusted.
- Monitor logs, storage growth, failed jobs, and backup freshness.


---
## References
| Topic | Documentation |
|---|---|
| What is Gitea? | [docs.gitea.com](https://docs.gitea.com/) |
| Installation with containers | [Installation guide](https://docs.gitea.com/1.26/installation/install-with-docker) |
| Pull requests | [Pull request documentation](https://docs.gitea.com/usage/pull-request) |
| Protected branches | [Protected branches](https://docs.gitea.com/usage/access-control/protected-branches) |
| Actions | [Quick start](https://docs.gitea.com/usage/actions/quickstart) · [Token permissions](https://docs.gitea.com/usage/actions/token-permissions) |
| Package registry | [Overview](https://docs.gitea.com/usage/packages/overview/) · [Generic packages](https://docs.gitea.com/usage/packages/generic) |
| Operations | [Backup and restore](https://docs.gitea.com/administration/backup-and-restore) · [Upgrade guide](https://docs.gitea.com/installation/upgrade-from-gitea) |

---
<!-- _class: lead -->
<!-- paginate: false -->

# Gitea in 15 Minutes
### Run your own collaborative Git service

 Patrick Stein
