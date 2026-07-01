# AGENTS.md

Guidance for AI agents working in `ansible-securityonion`.

## Overview

Ansible role (Galaxy: `jahrik.securityonion`) that stages a
[Security Onion](https://securityonion.net/) appliance install on Ubuntu: adds the legacy
`securityonion/stable` PPA, installs `securityonion-all`, and templates `/etc/nsm/sosetup.conf`.
`playbook.yml` at the repo root is a thin wrapper that `include_role`s this role by directory, so
`ansible-playbook playbook.yml` keeps working without the repo being checked out under a
`roles/` path.

## Task Flow

`tasks/main.yml` is flat (no install/uninstall split): add the PPA, disable the interactive MySQL
prompt, install packages, template `sosetup.conf` (`no_log: true` — it carries a credential).

**The appliance-install tasks below that point are commented out on purpose.** Running `sosetup`
unattended reconfigures partitions/services on a live box, and the PPA is long dead — there's no
safe way to converge it in CI or Molecule. Keep those tasks as commented reference; don't
uncomment them without validating against a current Security Onion release first.

## Conventions

- **Idempotency:** every active task must be safe to re-run.
- **Dependencies:** use `uv` for Python package management; do not use `pip` directly.
- **OS constraint:** all active tasks are guarded by `when: ansible_facts['distribution'] == 'Ubuntu'`.
- **Facts:** use `ansible_facts['<fact>']`, never the bare `ansible_<fact>` magic vars;
  `ansible.cfg` sets `inject_facts_as_vars = False`.
- **FQCN:** all modules fully qualified (`ansible.builtin.*`, `community.general.*`).
- **Secrets:** `master.sguil_client_password_1` defaults to `CHANGEME` in `defaults/main.yml` —
  override via Ansible Vault in a real `group_vars`/`host_vars` file, never commit a real value.
- **General rules:** abide by the global `AGENTS.md` guidelines (no hardcoded secrets or IPs).

## Testing

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
ansible-playbook playbook.yml --syntax-check
```

## CI/CD

- **lint** — `yamllint` + `ansible-lint` (profile `production`).
- **syntax** — `ansible-playbook playbook.yml --syntax-check`. No Molecule/converge job: the real
  install touches a dead PPA and reconfigures a live appliance, so there's nothing safe to
  converge in a container. Lint + syntax-check is the CI gate for this repo.
- **release** — `needs: [lint, syntax]`, publishes to Ansible Galaxy on merge to `main`.
