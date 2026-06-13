# ansible-role-ufw

[![CI](https://github.com/pluggero/ansible-role-ufw/actions/workflows/ci.yml/badge.svg)](https://github.com/pluggero/ansible-role-ufw/actions/workflows/ci.yml) [![Ansible Galaxy downloads](https://img.shields.io/ansible/role/d/pluggero/ufw?label=Galaxy%20downloads&logo=ansible&color=%23096598)](https://galaxy.ansible.com/ui/standalone/roles/pluggero/ufw)

Installs and configures UFW (Uncomplicated Firewall) on ArchLinux and Debian.

## Requirements

**Collections:**

- `ansible.builtin` (included with Ansible)
- `community.general` (for UFW module and ArchLinux support)

**Role dependencies:**

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
ufw_install_method: "package"
```

Installation method. Only `"package"` is supported.

```yaml
ufw_enabled: true
```

Start UFW and enable it on boot. Set to `false` to disable and stop UFW.

```yaml
ufw_logging: "off"
```

UFW logging level. Valid values: `"off"`, `"low"`, `"medium"`, `"high"`, `"full"`. Defaults to `"low"` — logs blocked packets without flooding the journal.

```yaml
ufw_manage_config: false
```

Set to `true` to manage `/etc/default/ufw` using the `ufw_config` dict.

```yaml
ufw_config:
  IPV6: "yes"
  DEFAULT_INPUT_POLICY: "DROP"
  DEFAULT_OUTPUT_POLICY: "ACCEPT"
  DEFAULT_FORWARD_POLICY: "DROP"
  DEFAULT_APPLICATION_POLICY: "SKIP"
```

UFW configuration applied to `/etc/default/ufw`. Only takes effect when `ufw_manage_config: true`.

```yaml
ufw_rules:
  - rule: allow
    to_port: "22"
    proto: tcp
    comment: "Allow SSH"
```

List of firewall rules to apply. The `rule` key is required; all others are optional. The default includes an SSH allow rule to prevent operator lockout on first enable.

**Rule dict keys:**

| Key         | Values                        | Description                   |
| ----------- | ----------------------------- | ----------------------------- |
| `rule`      | allow / deny / reject / limit | **Required.** The rule action |
| `to_port`   | port number or name           | Destination port              |
| `from_port` | port number or name           | Source port                   |
| `proto`     | tcp / udp / any               | Protocol                      |
| `from_ip`   | IP / CIDR / any               | Source address                |
| `to_ip`     | IP / CIDR / any               | Destination address           |
| `interface` | interface name                | Network interface             |
| `direction` | in / out / routed             | Traffic direction             |
| `log`       | boolean                       | Log matched packets           |
| `comment`   | string                        | Rule description              |
| `route`     | boolean                       | Routed/forwarded traffic rule |
| `insert`    | integer                       | Insert at position            |
| `delete`    | boolean                       | Delete this rule              |

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: pluggero.ufw
      vars:
        ufw_enabled: true
        ufw_logging: "low"
        ufw_manage_config: true
        ufw_config:
          IPV6: "yes"
          DEFAULT_INPUT_POLICY: "DROP"
          DEFAULT_OUTPUT_POLICY: "ACCEPT"
          DEFAULT_FORWARD_POLICY: "DROP"
        ufw_rules:
          - rule: allow
            to_port: "22"
            proto: tcp
            comment: "Allow SSH"
          - rule: allow
            to_port: "80"
            proto: tcp
            comment: "Allow HTTP"
          - rule: allow
            from_ip: "10.0.0.0/8"
            comment: "Allow internal network"
```

## License

MIT

## Author Information

This role was created in 2026 by pluggero.
