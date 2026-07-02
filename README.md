# jahrik.securityonion

[![CI/CD](https://github.com/jahrik/ansible-securityonion/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-securityonion/actions/workflows/cicd.yml)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-jahrik.securityonion-blue?logo=ansible)](https://galaxy.ansible.com/ui/standalone/roles/jahrik/securityonion/)

Stages a [Security Onion](https://securityonion.net/) appliance install on Ubuntu: adds the
`securityonion/stable` PPA, disables the interactive MySQL prompt, installs the
`securityonion-all` metapackage, and templates `/etc/nsm/sosetup.conf` for the setup wizard.

**The full appliance install is intentionally disabled.** Running `sosetup` unattended
reconfigures partitions and services on a live box, and the upstream PPA this role targets is
long dead — there's nothing safe to converge end-to-end in CI. `tasks/main.yml` keeps the
wizard invocation, firewall rule, and `local.rules` deployment as commented-out reference tasks;
running `sosetup` itself is a deliberate manual step after this role finishes staging the host.

## Usage

Include the role in your playbook:

```yaml
- hosts: all
  become: true
  roles:
    - jahrik.securityonion
```

Or run the bundled thin wrapper directly:

```bash
ansible-playbook -i <your-inventory> playbook.yml
```

Set real values (interfaces, IPs, credentials) in your own `group_vars`/`host_vars`, and put
`sguil_client_password_1` in Ansible Vault — never commit a real password.

## Variables

Key variables (see `defaults/main.yml` for the full list):

| Variable | Default | Description |
|---|---|---|
| `securityonion_ppa` | `ppa:securityonion/stable` | PPA added on Ubuntu hosts. |
| `securityonion_packages` | `[syslog-ng-core, securityonion-all]` | Packages installed. |
| `management.mgmt_interface` | `eth0` | Management NIC for `sosetup.conf`. |
| `sniffing.sniffing_interfaces` | `[eth1]` | Interfaces to sniff. |
| `master.sguil_client_password_1` | `CHANGEME` | Sguil/Squert/ELSA/Snorby password — override via Vault. |

## Manual steps after this role runs

1. Install a fresh Ubuntu host (do NOT enable disk/home encryption or automatic updates).
2. Run this role to stage the repo, packages, and `/etc/nsm/sosetup.conf`.
3. SSH in and run: `sudo sosetup -f /etc/nsm/sosetup.conf`
4. Review the Security Onion Post-Installation docs.

## Testing

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
ansible-playbook playbook.yml --syntax-check
```

CI runs lint and a syntax check only — see [AGENTS.md](AGENTS.md) for why.

## License

MIT

## Author

jahrik@gmail.com
