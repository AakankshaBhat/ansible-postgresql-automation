# Ansible PostgreSQL Automation

## Project Overview

This project automates the installation, configuration, and basic provisioning of PostgreSQL on an Ubuntu Server using Ansible.

The objective is to build a reusable and repeatable PostgreSQL deployment workflow using Ansible playbooks, roles, and inventory-based variables.

## Architecture

- **Control Node:** macOS running Ansible
- **Managed Node:** Ubuntu Server virtual machine
- **Database:** PostgreSQL
- **Automation:** Ansible
- **Connection:** SSH
- **Privilege Escalation:** Ansible `become`

## What This Project Automates

1. Updates the Ubuntu package cache.
2. Installs PostgreSQL and supporting packages.
3. Ensures the PostgreSQL service is enabled and running.
4. Configures PostgreSQL parameters:
   - `max_connections`
   - `shared_buffers`
5. Creates an application database named `appdb`.
6. Creates an application role named `app_user` with LOGIN capability.
7. Grants database-level privileges to the application role.

## Project Structure

```text
ansible-postgres-automation/
├── ansible.cfg
├── inventory/
│   ├── hosts.ini
│   └── group_vars/
│       └── postgres_servers.yml
├── playbooks/
│   └── site.yml
└── roles/
    ├── postgres_install/
    │   └── tasks/
    │       └── main.yml
    ├── postgres_config/
    │   ├── tasks/
    │   │   └── main.yml
    │   └── handlers/
    │       └── main.yml
    └── postgres_provision/
        └── tasks/
            └── main.yml
```

## Prerequisites

- macOS or another system capable of running Ansible
- Ansible installed on the control node
- Ubuntu Server managed node
- SSH connectivity between the control node and managed node
- `community.postgresql` Ansible collection

To ensure  required collection is installed:

```bash
ansible-galaxy collection install community.postgresql
```

## Configuration

The managed host is defined in:

`inventory/hosts.ini`

PostgreSQL configuration variables are maintained in:

`inventory/group_vars/postgres_servers.yml`

Example:

```yaml
---
postgresql_config:
  max_connections: 150
  shared_buffers: 128MB
```

Update the inventory with the IP address and SSH username of your own Ubuntu server.

## Deployment

Run the complete deployment from the project root:

```bash
ansible-playbook playbooks/site.yml -K
```

The `-K` option prompts for the privilege-escalation password when required.

The main playbook orchestrates the following roles:

1. `postgres_install`
2. `postgres_config`
3. `postgres_provision`

## Validation

Connect to the managed Ubuntu server and open PostgreSQL:

```bash
sudo -u postgres psql
```

Verify the configuration:

```sql
SHOW max_connections;
SHOW shared_buffers;
```

Verify the database and role:

```sql
SELECT datname FROM pg_database WHERE datname = 'appdb';

SELECT rolname, rolcanlogin
FROM pg_roles
WHERE rolname = 'app_user';
```

## Idempotency

The deployment was executed more than once to validate idempotency.

The successful subsequent execution reported:

```text
ok=8
changed=0
unreachable=0
failed=0
```

This demonstrates that Ansible did not make unnecessary changes when the managed server was already in the desired state.

## Key Concepts Demonstrated

- Ansible inventory management
- YAML-based configuration
- Ansible playbooks
- Reusable Ansible roles
- Group variables
- Privilege escalation
- PostgreSQL configuration management
- PostgreSQL database and role provisioning
- Handlers
- Idempotent automation

## Future Improvements

- Secure database credentials using Ansible Vault
- Make database names and users configurable through variables
- Implement least-privilege database access
- Add PostgreSQL validation and health checks
- Add backup and recovery automation
- Extend the setup to a multi-node PostgreSQL environment
- Explore PostgreSQL deployment on Kubernetes

## Project Status

**Completed:** Single-node PostgreSQL installation, configuration, and basic database provisioning using reusable Ansible roles.

**Next:** Improve security, validation, and automation capabilities.
