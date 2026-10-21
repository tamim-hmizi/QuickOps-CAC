# QuickOps CAC

QuickOps CAC is the Configuration as Code layer of QuickOps, a DevOps-as-a-Service platform. Its Ansible roles turn the VM provisioned by QuickOps-IAC into the QuickOps toolchain host: Docker, Jenkins, Nexus, SonarQube with PostgreSQL, Trivy, Terraform, Prometheus and Grafana.

## Features

- Single playbook (`playbooks/main.yml`) applying the roles in order to the `vm` host
- Roles:
  - `config`, `ansible`: base system configuration and Ansible on the host
  - `terraform`: installs a pinned Terraform CLI
  - `docker`: Docker engine
  - `nexus`, `jenkins`: Sonatype Nexus 3 and Jenkins containers
  - `trivy`: installs a pinned, checksum-verified Trivy binary
  - `network_sonarqube`, `postgresql_sonarqube`, `sonarqube`: Docker network, PostgreSQL database and SonarQube container
  - `prometheus`: Prometheus container with a Jinja-templated `prometheus.yml`
  - `grafana`: Grafana container with a Jinja-templated `grafana.ini`
- Handlers for service restarts and installation checks, defaults per role, ansible-lint configuration

## Tech stack

Ansible, Jinja2, Docker (`community.docker` collection), Ubuntu.

## Architecture

```
ansible.cfg                 Inventory, roles and collections paths, SSH settings
inventories/inventory.yaml  Target host (vm)
playbooks/main.yml          Main playbook
requirements.yml            Ansible Galaxy collections
roles/<role>/               tasks, handlers, defaults, templates
```

The QuickOps Backend uses the tools installed here (Jenkins, SonarQube, Nexus, Prometheus, Grafana) to build, scan, store and monitor user applications.

## Related repositories

| Repository | Role |
|---|---|
| [QuickOps-Frontend](https://github.com/tamim-hmizi/QuickOps-Frontend) | React web console |
| [QuickOps-Backend](https://github.com/tamim-hmizi/QuickOps-Backend) | Express/MongoDB API and deployment orchestrator |
| [QuickOps-AI](https://github.com/tamim-hmizi/QuickOps-AI) | FastAPI + LLM deployment recommender |
| [QuickOps-IAC](https://github.com/tamim-hmizi/QuickOps-IAC) | Terraform for the QuickOps platform VM |
| [QuickOps-CAC](https://github.com/tamim-hmizi/QuickOps-CAC) | Ansible configuration of the DevOps toolchain |

## Getting started

### Prerequisites

- Ansible (ansible-core 2.15 or later) on the control node
- SSH access to the target VM with sudo rights

### Configuration

- Set the target host, user and SSH key path in `inventories/inventory.yaml`
- Adjust role defaults in `roles/<role>/defaults/main.yml` (ports, versions)
- Override the default SonarQube database and Grafana admin credentials before any real use (for example with Ansible Vault)

### Usage

```bash
ansible-galaxy collection install -r requirements.yml
ansible-playbook playbooks/main.yml --syntax-check
ansible-lint
ansible-playbook playbooks/main.yml
```

Default ports: Jenkins 8080, Nexus 8081, SonarQube 9000, Prometheus 9090, Grafana 3000.

## Project context

RFC final year project, 2025, by Tamim Hmizi: https://tamim-hmizi.github.io/Portfolio/
