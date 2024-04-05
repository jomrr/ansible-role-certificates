# Ansible role certificates

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-certificates) ![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-certificates) ![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-certificates)

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
| Alpine | Alpine | 3.18 | [jomrr/molecule-alpine:3.18]( https://hub.docker.com/r/jomrr/molecule-alpine ) |
| | | 3.19 | [jomrr/molecule-alpine:3.19]( https://hub.docker.com/r/jomrr/molecule-alpine ) |
| | | latest | [jomrr/molecule-alpine:latest]( https://hub.docker.com/r/jomrr/molecule-alpine ) |
| Archlinux | ArchLinux | latest | [jomrr/molecule-archlinux:latest]( https://hub.docker.com/r/jomrr/molecule-archlinux ) |
| Amazon | AmazonLinux | 2 | [jomrr/molecule-amazonlinux:2]( https://hub.docker.com/r/jomrr/molecule-amazonlinux ) |
| | | 2023 | [jomrr/molecule-amazonlinux:2023]( https://hub.docker.com/r/jomrr/molecule-amazonlinux ) |
| | | latest | [jomrr/molecule-amazonlinux:latest]( https://hub.docker.com/r/jomrr/molecule-amazonlinux ) |
| Debian | Debian | 11 | [jomrr/molecule-debian:11]( https://hub.docker.com/r/jomrr/molecule-debian ) |
| | | 12 | [jomrr/molecule-debian:12]( https://hub.docker.com/r/jomrr/molecule-debian ) |
| | | latest | [jomrr/molecule-debian:latest]( https://hub.docker.com/r/jomrr/molecule-debian ) |
| | | testing | [jomrr/molecule-debian:testing]( https://hub.docker.com/r/jomrr/molecule-debian ) |
| Debian | Ubuntu | 20.04 | [jomrr/molecule-ubuntu:20.04]( https://hub.docker.com/r/jomrr/molecule-ubuntu ) |
| | | 22.04 | [jomrr/molecule-ubuntu:22.04]( https://hub.docker.com/r/jomrr/molecule-ubuntu ) |
| | | latest | [jomrr/molecule-ubuntu:latest]( https://hub.docker.com/r/jomrr/molecule-ubuntu ) |
| RedHat | AlmaLinux | 8 | [jomrr/molecule-almalinux:8]( https://hub.docker.com/r/jomrr/molecule-almalinux ) |
| | | 9 | [jomrr/molecule-almalinux:9]( https://hub.docker.com/r/jomrr/molecule-almalinux ) |
| | | latest | [jomrr/molecule-almalinux:latest]( https://hub.docker.com/r/jomrr/molecule-almalinux ) |
| RedHat | Fedora | 39 | [jomrr/molecule-fedora:39]( https://hub.docker.com/r/jomrr/molecule-fedora ) |
| | | latest | [jomrr/molecule-fedora:latest]( https://hub.docker.com/r/jomrr/molecule-fedora ) |
| | | rawhide | [jomrr/molecule-fedora:rawhide]( https://hub.docker.com/r/jomrr/molecule-fedora ) |
| RedHat | OracleLinux | 8 | [jomrr/molecule-oraclelinux:8]( https://hub.docker.com/r/jomrr/molecule-oraclelinux ) |
| | | 9 | [jomrr/molecule-oraclelinux:9]( https://hub.docker.com/r/jomrr/molecule-oraclelinux ) |
| | | latest | [jomrr/molecule-oraclelinux:latest]( https://hub.docker.com/r/jomrr/molecule-oraclelinux ) |

## Role Variables

No role default variables specified, see [defaults/main.yml](defaults/main.yml).

## Example Playbook

Example(s) of how to use this role:

## The default example playbook

The default example playbook for the role.

```yaml
---
# name: "certificates"
# file: "site.yml"
- name: "PLAYBOOK | certificates | distribute certificates"
  hosts: all
  gather_facts: true
  vars:
    certificates_host: "certificates_ca_host"
    certificates_ca_certs:
      - src: "/etc/ssl/ca/example/pub/root-ca.pem"
        dest: "{{ certificates_ca_trust_dir }}/example-root-ca.crt"
      - src: "/etc/ssl/ca/example/pub/intermediate-ca.pem"
        dest: "{{ certificates_ca_trust_dir }}/example-intermediate-ca.crt"
      - src: "/etc/ssl/ca/example/pub/component-ca.pem"
        dest: "{{ certificates_ca_trust_dir }}/example-component-ca.crt"
      - src: "/etc/ssl/ca/example/pub/identity-ca.pem"
        dest: "{{ certificates_ca_trust_dir }}/example-identity-ca.crt"
    certificates_certs:
      - src: "/etc/ssl/ca/example/dist/fritzbox.pem"
        dest: "{{ certificates_tls_dir }}/certs/fritzbox.pem"
        mode: "0644"
      - src: "/etc/ssl/ca/example/dist/fritzbox-fullchain.pem"
        dest: "{{ certificates_tls_dir }}/certs/fritzbox-fullchain.pem"
        mode: "0644"
      - src: "/etc/ssl/ca/example/dist/fritzbox.key"
        dest: "{{ certificates_tls_dir }}/private/fritzbox.key"
        mode: "0600"
  roles:
    - role: "jomrr.certificates"

- name: "PLAYBOOK | certificates | cleanup controller"
  hosts: localhost
  vars:
    certificates_ca_trust_dir: /tmp   # just a dummy, as we use basename
    certificates_tls_dir: /tmp        # just a dummy, as we use basename
  tasks:
    - name: "TASK | certificates | Remove /tmp files"
      ansible.builtin.file:
        path: "/tmp/{{ item | ansible.builtin.basename }}"
        state: absent
      loop: "{{ certificates_ca_certs | map(attribute='dest') | list + certificates_certs | map(attribute='dest') | list }}"
```

## License

This role is published under the [MIT License](LICENSE).

## Author Information

This role was created in 2024 by Jonas Mauer (@jomrr).

## Contributing

Contributions to this role are welcome.
Please follow the steps described in [CONTRIBUTING.md](CONTRIBUTING.md).

## References

No references.

---
