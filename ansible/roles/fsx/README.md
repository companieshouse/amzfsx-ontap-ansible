# Role: FSx | amzfsx-ontap-ansible

Configures an [Amazon FSx for NetApp ONTAP](https://aws.amazon.com/fsx/netapp-ontap/)
file system:
DNS,
AD-joined CIFS server with encrypted DC connections,
and domain admin user reconciliation.

## Requirements

- Ansible `>= 2.15.6`
- Python: `requests`, `netapp-lib`
- Control node must reach the FSx management endpoint; the SVM must reach AD
- AD account with rights to create computer objects in the target OU

Collection dependencies (`community.hashi_vault`, `netapp.ontap`) install automatically with the collection.

## Variables

Defined in [`meta/argument_specs.yml`](meta/argument_specs.yml). View with:
```
ansible-doc -t role companieshouse.infrastructure.fsx
```

Required:
- `netapp_hostname`
- `netapp_username`
- `netapp_password`
- `cifs_server_name`
- `dns_servers`
- `dc_admin_user_name`
- `dc_admin_password`
- `fsx_domain_admins`

Defaulted: `vserver` (`nfs_svm`), `cifs_domain` (`companieshouse.local`), `dc_ou` (Companies House Tier 0).

## Usage

```yaml
- hosts: localhost
  gather_facts: false
  roles:
    - role: companieshouse.infrastructure.vault
    - role: companieshouse.infrastructure.fsx
```

Without the `vault` role, you need to supply variables yourself e.g. extra-vars, group_vars, ansible-vault. The fsx argument-spec validation will fail loudly if anything is missing.


## CIFS server renames

The role won't rename an existing CIFS server. To rename, clean up the old object in AD, then
set your desired `cifs_server_name`.
