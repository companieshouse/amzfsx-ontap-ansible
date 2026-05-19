# Role: Vault | amzfsx-ontap-ansible

Loads FSx credentials and variables from HashiCorp Vault and exposes them as Ansible facts used in the `fsx` role.

All Vault lookups use `no_log: true`.

## Requirements

- Ansible `>= 2.15.6`
- `community.hashi_vault` collection (in `requirements.yml`)
- A HashiCorp Vault server reachable from the control node
- An AppRole with read access to the paths under `vault_base_path`

## Variables

Two extra-vars must be provided in order to build the `vault_base_path`:

- `aws_profile`
- `fsx_name`

The path is constructed in `defaults/main.yml` as:
```
/applications/{{ aws_profile }}/amzfsx/{{ fsx_name }}
```

## Environment variables

AppRole authentication requires all three to be exported:
- `ANSIBLE_HASHI_VAULT_ADDR`
- `ANSIBLE_HASHI_VAULT_ROLE_ID`
- `ANSIBLE_HASHI_VAULT_SECRET_ID`

The role checks these are present before attempting any lookups.

## Vault layout

The role reads three KV2 secrets relative to `vault_base_path`:

| Path | Fields |
|---|---|
| `/config` | `netapp_hostname`, `cifs_server_name`, `dns_servers` (JSON-encoded list) |
| `/credentials` | `fsxadmin_username`, `fsxadmin_password`, `ad_join_username`, `ad_join_password` |
| `/admins` | `domain_admins` (JSON-encoded list) |


## Facts set

| Fact | Source |
|---|---|
| `netapp_hostname` | `/config` |
| `cifs_server_name` | `/config` |
| `dns_servers` | `/config` (parsed from JSON) |
| `netapp_username` | `/credentials.fsxadmin_username` |
| `netapp_password` | `/credentials.fsxadmin_password` |
| `dc_admin_user_name` | `/credentials.ad_join_username` |
| `dc_admin_password` | `/credentials.ad_join_password` |
| `fsx_domain_admins` | `/admins` (parsed from JSON) |

## Usage

```yaml
- hosts: localhost
  gather_facts: false
  roles:
    - role: vault
      tags: [vault]
    - role: fsx
```

If you're providing local variables (e.g. extra-vars, ansible-vault), then you can skip this role with `--skip-tags vault`
