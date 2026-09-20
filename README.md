# Ansible Role: certificates

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-certificates)
![GitHub last commit](https://img.shields.io/github/last-commit/jomrr/ansible-role-certificates)
![GitHub issues](https://img.shields.io/github/issues-raw/jomrr/ansible-role-certificates)
[![dev](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-certificates/dev.yml?branch=dev&label=dev)](https://github.com/jomrr/ansible-role-certificates/actions/workflows/dev.yml?query=branch%3Adev)
[![main](https://img.shields.io/github/actions/workflow/status/jomrr/ansible-role-certificates/main.yml?branch=main&label=main)](https://github.com/jomrr/ansible-role-certificates/actions/workflows/main.yml?query=branch%3Amain)

Ansible role for distributing existing TLS certificates and notifying their
consuming services.

## Purpose

Obtain existing TLS artifacts from an explicit CA or artifact host and install
them on their consumer hosts. The role reads sources through delegated Ansible
connections, checks each certificate deployment, installs its files, and
notifies the explicitly assigned service handlers when resources change.
Issuance and renewal decisions remain with the issuer.

## Scope

### Managed

- Transfer of leaf certificates, optional issuer chains, fullchains and
  unencrypted private keys from a source host.
- Distribution-standard TLS directories and per-artifact file ownership and
  permissions on consumer hosts.
- Certificate/private-key public-key comparison and fullchain leaf identity
  checks before installation.
- Notifications to explicitly named handlers or listen topics in the same play,
  only on actual changes.

### Not Managed

- Issuance, renewal decisions, SANs, policies, algorithms, validity periods or
  CA passphrases.
- Service configuration, configuration checks, reload commands or service state.
- Trust-store installation, chain building, certificate revocation or trust-path
  validation.
- Removal of unlisted files, decryption of private keys or verification against
  an unspecified existing key.

## Requirements

- The controller's localhost Python interpreter needs cryptography for
  community.crypto metadata modules.
- The source host must be reachable through Ansible and have the declared
  artifact paths available.
- Artifact owners and groups must already exist. Service accounts need
  permission to traverse the platform private-key directory.
- Named service handlers must be loaded into the same play before certificates
  installs changed artifacts.

## Dependencies

```yaml
collections:
  - name: community.crypto
    version: '>=3.0.0'
  - name: community.general
    version: '>=12.0.0'
  - name: ansible.posix
    version: '>=2.0.0'
```

## Role Variables

### `certificates_certificates`

Type: `list`. Required: `false`.

Certificates assigned to this consumer. Each entry declares source artifacts,
per-file permissions and explicit handlers. Platform paths and name determine
destinations.

Default:

```yaml
certificates_certificates: []
```

### `certificates_owner`

Type: `str`. Required: `false`.

Default file owner, overridable separately on each artifact.

Default:

```yaml
certificates_owner: root
```

### `certificates_group`

Type: `str`. Required: `false`.

Default file group, overridable separately on each artifact.

Default:

```yaml
certificates_group: root
```

### `certificates_certificate_mode`

Type: `str`. Required: `false`.

Default certificate mode for managed resources; modes must be quoted octal
strings.

Default:

```yaml
certificates_certificate_mode: '0644'
```

### `certificates_private_key_mode`

Type: `str`. Required: `false`.

Default private key mode for managed resources; modes must be quoted octal
strings.

Default:

```yaml
certificates_private_key_mode: '0600'
```

### `certificates_source_host`

Type: `str`. Required: `true`.

Inventory host holding the existing artifacts. Each deployment may override
source_host.

### `certificates_source_become`

Type: `bool`. Required: `false`.

Read source files as root. Disable when the source connection account can read
all required artifacts.

Default:

```yaml
certificates_source_become: true
```

## Managed Files

- `RedHat family: /etc/pki/tls/certs for public artifacts and
  /etc/pki/tls/private for private keys.`
- `Debian family: /etc/ssl/certs for public artifacts and /etc/ssl/private for
  private keys.`
- `Suse family: /etc/ssl/servercerts for public artifacts and /etc/ssl/private
  for private keys.`
- `Filenames derived from name: <name>.crt, <name>-chain.pem,
  <name>-fullchain.pem and <name>.key.`

## Check Mode

Reads source artifacts, validates candidate pairs and reports destination
changes without installing files.

- Source artifacts must already exist, including during check mode.

## Service Behavior

Changed artifact content, ownership, permissions or SELinux type notifies the
explicit notify list on that certificate entry.

### Handlers

- Service roles own their configuration validation and reload or restart
  handlers.
- Handler execution follows the normal play boundaries; an explicit meta
  flush_handlers may establish an earlier activation boundary.

## Security Notes

- Private keys default to root ownership and mode 0600. Key acquisition,
  validation and copying suppress secret output and key diffs.
- Public files default to root:root and 0644; private keys to root:root and
  0600. Every artifact can override owner, group and mode separately.
- Artifacts travel through the controller over the configured Ansible
  connections. The role uses registered slurp results and creates no persistent
  controller staging files or cached facts. The controller must be trusted to
  handle end-entity keys.
- Source reads use root by default to access protected CA directories. Set
  certificates_source_become to false when the source connection account already
  has sufficient access, including an unprivileged localhost artifact source.
- Only declare end-entity key paths. CA private keys and CA passphrases are not
  distribution inputs.
- The role requests the SELinux policy default type for installed files.
  Standard paths do not replace service-specific SELinux or AppArmor access
  rules.

## Operational Notes

- Set certificates_source_host to an inventory host containing existing
  artifacts. A deployment may override source_host. This also supports other
  issuers and localhost as an artifact source. Host or group variables assign
  deployments to their consumers; no inventory group names or CA directory
  layout are built into the role.
- certificates_certificates is the only deployment list. Each entry requires
  name,
  certificate.src and notify. Optional chain, fullchain and private_key mappings
  require
  src and may override owner, group and mode. Source paths are absolute on the
  source
  host. Names are unique filename stems starting with an ASCII letter or digit
  and
  containing only letters, digits, dots, underscores or hyphens. Destination
  paths
  are derived from the platform and name; there is no separate directory list.
- Each deployment follows source reads, assertions and installation in that
  order. Its supplied private key must match the leaf certificate, and its
  supplied fullchain must start with that exact certificate. Installation uses
  the same bytes that were checked. RSA, EC and EdDSA keys are compared through
  their public keys. Without private_key, only public artifacts are installed.
- Issuer chains and fullchains are installed unchanged. Reading their first
  certificate checks that a PEM certificate is present; this does not verify
  chain order, trust, revocation or remaining validity. Issuance and service
  validation own those decisions.
- The role installs openssl to obtain the platform TLS directories and ensures
  the
  public certificate directory exists as root:root with mode 0755. Existing
  private-key
  directory permissions remain unchanged. Service roles manage account
  membership
  and access needed to traverse those directories. SUSE service certificates use
  /etc/ssl/servercerts because /etc/ssl/certs contains the generated trust
  store.
  An empty deployment list preserves previously installed certificate files.
- Use role-qualified handler names such as webserver : WEBSERVER | Activate TLS
  configuration,
  or a service role's documented listen topic. Load service handlers with roles,
  import_role,
  or a preceding include_role. Several changed files notify a shared handler
  only once
  at the next handler boundary. Do not flush between individual artifact
  installations.
  An explicit empty notify list installs files without requesting service
  activation.
- Run issuance and consumption as separate, ordered plays in one playbook. This
  provides a host barrier even when the consumer play uses the free strategy.
  Gate the job on issuance failure, for example with any_errors_fatal and
  strategy linear on the CA play. Source files must remain stable while read; do
  not concurrently renew the same artifacts. A failed deployment stops; the role
  does not roll back earlier deployments or force handlers.

## Supported Platforms

| OS Family | Distribution | Version | Container Image |
| --------- | ------------ | ------- | --------------- |
| RedHat | AlmaLinux | latest | [jomrr/molecule-almalinux:latest](https://hub.docker.com/r/jomrr/molecule-almalinux) |
| Debian | Debian | latest | [jomrr/molecule-debian:latest](https://hub.docker.com/r/jomrr/molecule-debian) |
| RedHat | Fedora | latest | [jomrr/molecule-fedora:latest](https://hub.docker.com/r/jomrr/molecule-fedora) |
| Suse | OpenSuse Leap | latest | [jomrr/molecule-opensuse-leap:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-leap) |
| Suse | OpenSuse Tumbleweed | latest | [jomrr/molecule-opensuse-tumbleweed:latest](https://hub.docker.com/r/jomrr/molecule-opensuse-tumbleweed) |
| Debian | Ubuntu | latest | [jomrr/molecule-ubuntu:latest](https://hub.docker.com/r/jomrr/molecule-ubuntu) |

## Example Playbook

### Deploy files from any artifact host

Assign this data to the intended consumer hosts. For example, the leaf becomes
/etc/pki/tls/certs/web.crt on RedHat, /etc/ssl/certs/web.crt on Debian,
or /etc/ssl/servercerts/web.crt on SUSE. The service role configures those
paths and supplies the named activation handler.

```yaml
certificates_source_host: artifacts01
certificates_certificates:
  - name: web
    certificate:
      src: /srv/tls/web.pem
    chain:
      src: /srv/tls/issuer-chain.pem
    fullchain:
      src: /srv/tls/web-fullchain.pem
    private_key:
      src: /srv/tls/web.key
      owner: root
      group: root
      mode: '0600'
    notify: ['webserver : WEBSERVER | Activate TLS configuration']
```

### Issue with ca and distribute in one playbook

Inventory explicitly sets ca_base_dir, ca_authorities and
ca_certificates for pki01, and requests the fullchain format for web01.
The illustrative webserver role supplies the handler, checks its
configuration and reloads or restarts the service. certificates
obtains the artifacts from pki01.

```yaml
- name: Issue or renew on the CA host
  hosts: pki01
  gather_facts: true
  strategy: linear
  any_errors_fatal: true
  roles:
    - role: jomrr.ca

- name: Obtain, install and activate on the web consumers
  hosts: web_consumers
  gather_facts: true
  vars:
    certificates_source_host: pki01
    certificates_certificates:
      - name: web
        certificate:
          src: "{{ hostvars['pki01'].ca_base_dir }}/certs/web01/web01.pem"
        fullchain:
          src: "{{ hostvars['pki01'].ca_base_dir }}/certs/web01/web01-fullchain.pem"
        private_key:
          src: "{{ hostvars['pki01'].ca_base_dir }}/certs/web01/web01.key"
        notify: ['webserver : WEBSERVER | Activate TLS configuration']
  tasks:
    - name: Obtain and install TLS artifacts
      ansible.builtin.import_role:
        name: jomrr.certificates

    - name: Configure and start the consumer service
      ansible.builtin.import_role:
        name: webserver

    - name: Activate validated changes
      ansible.builtin.meta: flush_handlers

```

### Use standard TLS paths with Samba AD DC

For a RedHat-family consumer, install dc01 into the standard TLS paths
and configure the existing samba_ad_dc TLS variables with absolute paths.
The example ca.pem contains the CA certificate Samba should trust;
fullchain.pem starts with the server certificate and includes intermediates.
Samba requires its unencrypted private key to belong to the effective
Samba user (normally root) with mode 0600. On Debian or SUSE use the
corresponding platform paths from Managed Files.

```yaml
certificates_source_host: pki01
certificates_certificates:
  - name: dc01
    certificate:
      src: /srv/tls/dc01/cert.pem
    chain:
      src: /srv/tls/dc01/ca.pem
    fullchain:
      src: /srv/tls/dc01/fullchain.pem
    private_key:
      src: /srv/tls/dc01/key.pem
      owner: root
      group: root
      mode: '0600'
    notify: ['restart domain controller']

samba_ad_dc_tls_certfile: /etc/pki/tls/certs/dc01-fullchain.pem
samba_ad_dc_tls_keyfile: /etc/pki/tls/private/dc01.key
samba_ad_dc_tls_cafile: /etc/pki/tls/certs/dc01-chain.pem
```

## References

- [Ansible handler scope and role-qualified names](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html)
- [X.509 certificate information](https://docs.ansible.com/projects/ansible/latest/collections/community/crypto/x509_certificate_info_module.html)
- [Private-key information](https://docs.ansible.com/projects/ansible/latest/collections/community/crypto/openssl_privatekey_info_module.html)
- [Samba TLS certificate settings](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html#TLSCERTFILE)

## Author

[Jonas Mauer](https://github.com/jomrr)

## License

This project is licensed under the MIT License.
See [LICENSE](LICENSE) for the full license text.

Copyright (c) 2024-2026 Jonas Mauer.
