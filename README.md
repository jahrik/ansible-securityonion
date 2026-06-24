# ansible-securityonion

Ansible role/playbook for deploying and configuring [SecurityOnion](https://securityonion.net/).

This repository configures an Ubuntu host to run SecurityOnion. It adds the stable SecurityOnion PPA, ensures non-interactive database installation, installs the `securityonion-all` metapackage, and templates out `sosetup.conf` for the Setup wizard.

## Example Usage

Run the playbook against your inventory:

```bash
ansible-playbook -i inventory.ini playbook.yml
```

Example `inventory.ini`:

```ini
[onion]
onion ansible_host=192.168.10.105
```

## Testing and Development

We use `uv` for Python dependency management and `molecule` for testing. To lint the code and run the test suite locally:

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
molecule test
```
