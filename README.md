# Ansible role certificates

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-certificates) ![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-certificates) ![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-certificates)

**Ansible role for deploying CA/TLS Certificates and updating the CA Trust Store.**

## Description

This role deploys CA certificates from the controller or a remote or web server
to configured destinations and updates the ca trust store on the target.
Furthermore it deploys TLS certificates and keys to the operating systems default location.


## Prerequisites

This role has no special prerequisites.

### System packages (Fedora)

- `python3` (>= 3.9)

### Python (requirements.txt)

- ansible >= 2.17

## Dependencies (requirements.yml)

This role has no dependencies.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
|-----------|--------------|---------|-----------------|
| RedHat | AlmaLinux | latest | [jomrr/molecule-almalinux:latest]( https://hub.docker.com/r/jomrr/molecule-almalinux ) |
| Alpine | Alpine | latest | [jomrr/molecule-alpine:latest]( https://hub.docker.com/r/jomrr/molecule-alpine ) |
| Archlinux | Archlinux | latest | [jomrr/molecule-archlinux:latest]( https://hub.docker.com/r/jomrr/molecule-archlinux ) |
| Debian | Debian | oldstable | [jomrr/molecule-debian:oldstable]( https://hub.docker.com/r/jomrr/molecule-debian ) |
| | | stable | [jomrr/molecule-debian:stable]( https://hub.docker.com/r/jomrr/molecule-debian ) |
| | | testing | [jomrr/molecule-debian:testing]( https://hub.docker.com/r/jomrr/molecule-debian ) |
| RedHat | Fedora | latest | [jomrr/molecule-fedora:latest]( https://hub.docker.com/r/jomrr/molecule-fedora ) |
| | | rawhide | [jomrr/molecule-fedora:rawhide]( https://hub.docker.com/r/jomrr/molecule-fedora ) |
| Suse | OpenSuse Leap | 15 | [jomrr/molecule-opensuse-leap:15]( https://hub.docker.com/r/jomrr/molecule-opensuse-leap ) |
| Debian | Ubuntu | latest | [jomrr/molecule-ubuntu:latest]( https://hub.docker.com/r/jomrr/molecule-ubuntu ) |

## Role Variables

No role default variables specified, see [defaults/main.yml](defaults/main.yml).

## Example Playbook

Example playbooks(s) that show how to use this role.

## Simple example playbook

A simple default example playbook for using jomrr.certificates.

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

## Author(s) and License

- :octocat:                 Author::    [jomrr](https://github.com/jomrr)
- :triangular_flag_on_post: Copyright:: 2024, Jonas Mauer
- :page_with_curl:          License::   [MIT](LICENSE)


---
