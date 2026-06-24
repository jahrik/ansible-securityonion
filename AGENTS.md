# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Overview

`ansible-securityonion` is an Ansible repository for deploying SecurityOnion. The configuration is primarily housed in `playbook.yml`. It targets Ubuntu systems.

## Workflow & Testing

When developing or validating changes in this repository, always run the standard linting and testing suite using `uv` and `molecule`:

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
molecule test
```

## Conventions

- **Idempotency:** Ensure all tasks can be run multiple times safely.
- **Dependencies:** Use `uv` for Python package management; do not use `pip` directly.
- **OS Constraints:** Apt repository and package installations should be guarded by `when: ansible_distribution == 'Ubuntu'`.
- **General Rules:** Abide by the global `AGENTS.md` guidelines (e.g., FQCN for Ansible modules, no hardcoded secrets or IPs).
