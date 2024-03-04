# Ansible role certificates

![GitHub](https://img.shields.io/github/license/certificates/ansible-role-certificates) ![GitHub last commit](https://img.shields.io/github/last-commit/certificates/ansible-role-certificates) ![GitHub issues](https://img.shields.io/github/issues-raw/certificates/ansible-role-certificates)

**Ansible role for deploying CA and issued certificates as well as updating the ca trust store.**

## Description

This role deploys CA certificates from a remote server to configured
destinations and updated the ca-trust on the OS. Furthermore it deploys
TLS certificates and keys to the operating systems default location for
TLS certificates and keys.

## Prerequisites

This role has no special prerequisites.

### System packages (Fedora)

- `python3` (Python 3.6 or later)

### Python (requirements.txt)

- ansible >= 2.15

## Dependencies (requirements.yml)

This role has no dependencies.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
|-----------|--------------|---------|-----------------|
| Alpine | Alpine | 3.18 | [jam82/molecule-alpine:3.18]( https://hub.docker.com/r/jam82/molecule-alpine ) |
| | | 3.19 | [jam82/molecule-alpine:3.19]( https://hub.docker.com/r/jam82/molecule-alpine ) |
| | | latest | [jam82/molecule-alpine:latest]( https://hub.docker.com/r/jam82/molecule-alpine ) |
| Archlinux | ArchLinux | latest | [jam82/molecule-archlinux:latest]( https://hub.docker.com/r/jam82/molecule-archlinux ) |
| Amazon | AmazonLinux | 2 | [jam82/molecule-amazonlinux:2]( https://hub.docker.com/r/jam82/molecule-amazonlinux ) |
| | | 2023 | [jam82/molecule-amazonlinux:2023]( https://hub.docker.com/r/jam82/molecule-amazonlinux ) |
| | | latest | [jam82/molecule-amazonlinux:latest]( https://hub.docker.com/r/jam82/molecule-amazonlinux ) |
| Debian | Debian | 11 | [jam82/molecule-debian:11]( https://hub.docker.com/r/jam82/molecule-debian ) |
| | | 12 | [jam82/molecule-debian:12]( https://hub.docker.com/r/jam82/molecule-debian ) |
| | | latest | [jam82/molecule-debian:latest]( https://hub.docker.com/r/jam82/molecule-debian ) |
| | | testing | [jam82/molecule-debian:testing]( https://hub.docker.com/r/jam82/molecule-debian ) |
| Debian | Ubuntu | 20.04 | [jam82/molecule-ubuntu:20.04]( https://hub.docker.com/r/jam82/molecule-ubuntu ) |
| | | 22.04 | [jam82/molecule-ubuntu:22.04]( https://hub.docker.com/r/jam82/molecule-ubuntu ) |
| | | latest | [jam82/molecule-ubuntu:latest]( https://hub.docker.com/r/jam82/molecule-ubuntu ) |
| RedHat | AlmaLinux | 8 | [jam82/molecule-almalinux:8]( https://hub.docker.com/r/jam82/molecule-almalinux ) |
| | | 9 | [jam82/molecule-almalinux:9]( https://hub.docker.com/r/jam82/molecule-almalinux ) |
| | | latest | [jam82/molecule-almalinux:latest]( https://hub.docker.com/r/jam82/molecule-almalinux ) |
| RedHat | Fedora | 39 | [jam82/molecule-fedora:39]( https://hub.docker.com/r/jam82/molecule-fedora ) |
| | | latest | [jam82/molecule-fedora:latest]( https://hub.docker.com/r/jam82/molecule-fedora ) |
| | | rawhide | [jam82/molecule-fedora:rawhide]( https://hub.docker.com/r/jam82/molecule-fedora ) |
| RedHat | OracleLinux | 8 | [jam82/molecule-oraclelinux:8]( https://hub.docker.com/r/jam82/molecule-oraclelinux ) |
| | | 9 | [jam82/molecule-oraclelinux:9]( https://hub.docker.com/r/jam82/molecule-oraclelinux ) |
| | | latest | [jam82/molecule-oraclelinux:latest]( https://hub.docker.com/r/jam82/molecule-oraclelinux ) |

## Role Variables

No role default variables specified, see [defaults/main.yml](defaults/main.yml).

## Example Playbook

Example(s) of how to use this role:

## The default example playbook

The default example playbook for the role.

```yaml
---
# name: "certificates"
# file: "certificates.yml"
- hosts: all
  gather_facts: true
  roles:
    - role: "jam82.certificates"

```

## License

This role is published under the [MIT License](LICENSE).

## Author Information

This role was created in 2024 by Jonas Mauer (@jam82).

## Contributing

Contributions to this role are welcome.
Please follow the steps described in [CONTRIBUTING.md](CONTRIBUTING.md).

## References

No references.

---
