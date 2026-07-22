---
marp: true
theme: default
size: 16:9
paginate: true
footer: Gitea in Practice · July 2026
style: |
  :root {
    --bg: #f7f5ef;
    --ink: #182026;
    --muted: #596169;
    --line: #d8d2c4;
    --accent: #0f766e;
    --accent-2: #b45309;
    --gitea: #609926;
    --panel: #ffffff;
    --code-bg: #15202b;
    --code-ink: #edf2f7;
  }

  section {
    background: var(--bg);
    color: var(--ink);
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    font-size: 27px;
    line-height: 1.25;
    padding: 64px 76px 58px;
  }

  section::before {
    border-top: 5px solid var(--accent);
    content: "";
    left: 76px;
    position: absolute;
    right: 76px;
    top: 34px;
  }

  section.lead {
    justify-content: center;
    text-align: left;
  }

  section.lead.center {
    text-align: center;
  }

  h1 {
    color: var(--ink);
    font-size: 66px;
    letter-spacing: -0.035em;
    line-height: 0.98;
    margin: 0 0 22px;
  }

  h2 {
    color: var(--muted);
    font-size: 42px;
    font-weight: 600;
    letter-spacing: -0.02em;
    line-height: 1.08;
    margin: 0 0 30px;
  }

  h3 {
    color: var(--ink);
    font-size: 28px;
    line-height: 1.12;
    margin: 0 0 9px;
  }

  p { margin: 0 0 14px; }
  strong { color: var(--ink); }
  a { color: var(--accent); }

  .kicker {
    color: var(--accent-2);
    font-size: 25px;
    font-weight: 800;
    letter-spacing: 0.05em;
    margin: 0 0 8px;
    text-transform: uppercase;
  }

  .lede {
    color: var(--muted);
    font-size: 30px;
    line-height: 1.3;
    max-width: 760px;
  }

  ul, ol {
    margin: 0;
    padding-left: 1.25em;
  }

  li { margin-bottom: 12px; }
  li::marker { color: var(--gitea); font-weight: 800; }

  code {
    background: rgba(216, 210, 196, 0.52);
    color: var(--ink);
    font-family: "SFMono-Regular", Consolas, "Liberation Mono", monospace;
  }

  pre {
    background: var(--code-bg);
    border-radius: 9px;
    color: var(--code-ink);
    font-size: 20px;
    line-height: 1.35;
    padding: 24px 28px;
  }

  pre code { background: transparent; color: inherit; }

  table {
    border-collapse: separate;
    border-spacing: 12px;
    display: table;
    font-size: 22px;
    margin: 0 -12px;
    width: calc(100% + 24px);
  }

  th, td {
    background: var(--panel);
    border: 1px solid var(--line) !important;
    border-radius: 8px;
    padding: 18px 20px;
    text-align: left;
    vertical-align: top;
  }

  th {
    border-top: 5px solid var(--gitea) !important;
    color: var(--ink);
    font-size: 25px;
  }

  .columns {
    align-items: center;
    display: grid;
    gap: 34px;
    grid-template-columns: 1fr 1fr;
  }

  .card {
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: 8px;
    padding: 25px 28px;
  }

  .metric {
    color: var(--gitea);
    font-size: 66px;
    font-weight: 850;
    line-height: 1;
    margin-bottom: 12px;
  }

  .flow th, .flow td { text-align: center; }
  .flow td { color: var(--muted); font-size: 19px; }

  .source {
    color: var(--muted);
    font-size: 16px;
    line-height: 1.25;
    margin-top: 20px;
  }

  .compact { font-size: 24px; }
  .compact h2 { margin-bottom: 20px; }
  .compact pre { font-size: 18px; }

  footer {
    color: var(--muted);
    font-size: 15px;
    left: 76px;
    right: auto;
  }

  section::after {
    color: var(--muted);
    font-size: 15px;
  }
---

<!-- _class: lead -->
<!-- _paginate: false -->
<!-- _footer: "" -->

![bg right:40% contain](images/gitea-logo.svg)

<p class="kicker">July 2026</p>

# Gitea in Practice

<p class="lede">Run your own collaborative Git service—and complete the whole path from issue to tested merge.</p>

---

<p class="kicker">Goal</p>

## Leave with a working mental model

- Start Gitea locally with persistent data.
- Move one change through issue, branch, pull request, review, and merge.
- Add a status check and protect `main`.
- Know what must change before production.

---

<p class="kicker">Agenda</p>

## One end-to-end workflow

1. What Gitea owns
2. Start an instance
3. Collaborate on a change
4. Automate and publish
5. Operate it safely

---

<p class="kicker">Mental model</p>

## Gitea is the forge around Git

| Git hosting | Collaboration | Automation | Distribution |
|---|---|---|---|
| Repositories over SSH and HTTP(S), with users, organizations, teams, and permissions. | Issues, pull requests, reviews, projects, releases, wikis, and notifications. | Gitea Actions plus external runners, webhooks, and a REST API. | Package registries for OCI images and common language ecosystems. |

<p class="source">Source: <a href="https://docs.gitea.com/">Gitea documentation — What is Gitea?</a></p>

---

<p class="kicker">Demo architecture</p>

## Start small; keep the data outside the container

| Browser | Gitea 1.26.4 | SQLite | `/data` | Git client |
|---|---|---|---|---|
| localhost:3000 | Web + SSH | Built in | Named volume | SSH on 2222 |

**The container is disposable. The volume is not.**

---

<!-- _class: compact -->

<p class="kicker">Start Gitea</p>

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

# Observe startup
container logs gitea
```

<p class="source">Adapted from the <a href="https://docs.gitea.com/1.26/installation/install-with-docker">official container installation guide</a>; commands use Apple <code>container</code>.</p>

---

<p class="kicker">First run</p>

## The installer turns runtime choices into configuration

<div class="columns">
<div>

- Database: SQLite3 for this demo
- Site title and server domain
- HTTP port `3000`
- SSH port `2222`
- Create the first administrator

</div>
<div class="card">

<p class="metric">/data</p>

### One directory to protect

The official image keeps configuration, repositories, attachments, indexes, and the SQLite database under the persistent data tree.

</div>
</div>

---

<p class="kicker">Repository setup</p>

## Organize ownership before adding code

| Organization | Teams | Repository |
|---|---|---|
| Stable owner for repositories, packages, runners, and shared identity. | Group people by responsibility and grant repository-unit permissions. | Choose visibility, initialize a README, then add collaborators and rules. |

<!--
Demo: create organization `tea-lab`, team `developers`, and repository `hello-gitea`.
-->

---

<!-- _class: compact -->

<p class="kicker">Everyday Git</p>

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

<!--
The push response links directly to the compare page for creating a pull request.
-->

---

<p class="kicker">Collaboration loop</p>

## Every change gains context and a decision trail

| Issue | Branch | Pull request | Review + CI | Merge |
|---|---|---|---|---|
| Why change? | Isolate work | Explain change | Check quality | Record decision |

<p class="source">Pull requests support comments, requested changes, approval, and merge. Source: <a href="https://docs.gitea.com/usage/pull-request">Gitea pull request documentation</a>.</p>

---

<p class="kicker">Pull request</p>

## Review the intent, the diff, and the evidence

| Intent | Diff | Evidence |
|---|---|---|
| Use a descriptive title, explain the reason, and link the issue with `Closes #1`. | Comment on exact lines, request changes, or approve the proposed branch. | Require tests and status checks before the merge button becomes available. |

<!--
Demo: open a pull request, request one tiny change, push the fix, approve, then merge.
-->

---

<p class="kicker">Branch protection</p>

## Turn the team agreement into an enforced rule

<div class="columns">
<div>

- Block direct pushes to `main`
- Require one approval
- Dismiss stale approvals
- Require successful status checks
- Apply rules to administrators

</div>
<div class="card">

### Settings → Branches

Create a rule for `main`. For release branches, use a pattern such as `release/*`. Put specific patterns ahead of broad fallbacks.

</div>
</div>

<p class="source">Source: <a href="https://docs.gitea.com/usage/access-control/protected-branches">Protected branches</a>.</p>

---

<!-- _class: compact -->

<p class="kicker">Gitea Actions</p>

## A workflow file turns every push into evidence

```yaml
# .gitea/workflows/test.yaml
name: test
on: [push, pull_request]

permissions:
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: test "$(cat greeting.txt)" = "Hello from Gitea"
```

<p class="source">Source: <a href="https://docs.gitea.com/usage/actions/quickstart">Gitea Actions quick start</a> and <a href="https://docs.gitea.com/usage/actions/token-permissions">job token permissions</a>.</p>

<!--
Enable Actions for the repository and register a Gitea runner. Prefer a separate runner host for real workloads.
-->

---

<p class="kicker">Runner boundary</p>

## The server schedules; the runner executes

| Push / PR | Gitea | Runner | Job | Status |
|---|---|---|---|---|
| Event | Queues job | Polls server | Runs isolated | Gates merge |

- Do not register the runner with `localhost`; job environments must be able to reach the instance URL.
- Treat runners as code-execution infrastructure, not as harmless agents.

---

<!-- _class: compact -->

<p class="kicker">Package registry</p>

## Publish build output beside the source and release

<div class="columns">
<div>

```sh
# Generic package example
export GITEA_TOKEN='…'

curl --user "patrick:$GITEA_TOKEN" \
  --upload-file hello.tar.gz \
  http://localhost:3000/api/packages/tea-lab/generic/hello/1.0.0/hello.tar.gz
```

</div>
<div class="card">

### One owner, many ecosystems

Gitea supports OCI containers, Swift, npm, Maven, NuGet, PyPI, Cargo, generic files, and many other package formats.

</div>
</div>

<p class="source">Source: <a href="https://docs.gitea.com/usage/packages/overview/">Package registry overview</a> and <a href="https://docs.gitea.com/usage/packages/generic">generic packages</a>.</p>

<!--
Use a personal access token with package write permission—not an account password.
-->

---

<!-- _class: compact -->

<p class="kicker">API</p>

## Automate the forge through stable interfaces

<div class="columns">
<div>

```sh
# List repositories for an organization
curl --header "Authorization: token $GITEA_TOKEN" \
  http://localhost:3000/api/v1/orgs/tea-lab/repos

# Built-in API explorer
open http://localhost:3000/api/swagger
```

</div>
<div>

- REST API for users, repositories, issues, pull requests, releases, and administration
- Webhooks for event-driven integration
- `tea` CLI for common Gitea operations

</div>
</div>

---

<p class="kicker">Production shape</p>

## The demo is intentionally not a production topology

| Database | Ingress | Identity | Storage |
|---|---|---|---|
| Use PostgreSQL or MySQL when operational scale, tooling, or availability requires it. | Terminate HTTPS at a reverse proxy and set `ROOT_URL`, domain, and SSH ports correctly. | Connect SMTP and your authentication provider; restrict registration and administrator access. | Plan capacity for repositories, LFS, attachments, Actions artifacts, packages, and backups. |

---

<p class="kicker">Backup</p>

## A backup is complete only when database and files agree

<div class="columns">
<div>

- Stop writes or take a consistent snapshot.
- Back up configuration and secrets.
- Back up repositories, attachments, LFS, packages, and Actions data.
- Back up the database with its native tooling.
- Restore into a test instance regularly.

</div>
<div class="card">

### Upgrade sequence

Read release notes → stage the new version → stop → back up → change the pinned image tag → start → verify migrations and core workflows.

</div>
</div>

<p class="source">Sources: <a href="https://docs.gitea.com/administration/backup-and-restore">Backup and Restore</a> and <a href="https://docs.gitea.com/installation/upgrade-from-gitea">Upgrade from an old Gitea</a>.</p>

---

<p class="kicker">Security baseline</p>

## Self-hosting moves responsibility to the operator

- Pin versions and apply security releases promptly.
- Use HTTPS, secure cookies, and a trusted reverse proxy configuration.
- Require 2FA where appropriate; use scoped tokens for automation.
- Protect default branches and restrict force pushes.
- Isolate Actions runners and treat pull-request code as untrusted.
- Monitor logs, storage growth, failed jobs, and backup freshness.

---

<p class="kicker">Practical exercise</p>

## Complete the loop yourself

1. Create an issue: “Add a greeting.”
2. Create a branch, commit a file, and push it.
3. Open a pull request that closes the issue.
4. Review the diff and merge it.
5. Protect `main`, then prove a direct push is rejected.

<!--
Stretch goal: add the workflow from the Actions slide and require its status check.
-->

---

<p class="kicker">Decision</p>

## Gitea fits when ownership matters more than outsourcing the forge

| Strong fit | Count the cost |
|---|---|
| ✓ Self-hosting and data control<br>✓ Familiar GitHub-like workflow<br>✓ Small operational footprint<br>✓ Integrated packages and CI | Upgrades, security, backups, and monitoring are yours.<br><br>Actions compatibility is broad, not identical to GitHub Actions.<br><br>High availability and large installations need deliberate architecture. |

---

<!-- _class: lead center -->
<!-- _paginate: false -->
<!-- _footer: "" -->

<p class="kicker">July 2026</p>

# Own the forge

<p class="lede"><strong>Keep the workflow familiar.</strong></p>

<p class="lede">Patrick Stein</p>

---

<!-- _class: compact -->

<p class="kicker">References</p>

## Official Gitea documentation used in this deck

| Topic | Documentation |
|---|---|
| What is Gitea? | [docs.gitea.com](https://docs.gitea.com/) |
| Installation with containers | [Installation guide](https://docs.gitea.com/1.26/installation/install-with-docker) |
| Pull requests | [Pull request documentation](https://docs.gitea.com/usage/pull-request) |
| Protected branches | [Protected branches](https://docs.gitea.com/usage/access-control/protected-branches) |
| Actions | [Quick start](https://docs.gitea.com/usage/actions/quickstart) · [Token permissions](https://docs.gitea.com/usage/actions/token-permissions) |
| Package registry | [Overview](https://docs.gitea.com/usage/packages/overview/) · [Generic packages](https://docs.gitea.com/usage/packages/generic) |
| Operations | [Backup and restore](https://docs.gitea.com/administration/backup-and-restore) · [Upgrade guide](https://docs.gitea.com/installation/upgrade-from-gitea) |

