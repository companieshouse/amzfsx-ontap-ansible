# amzfsx-ontap-ansible

An example project showing how to use the ansible-collection-infrastrucutre
collection's vault and fsx roles to configures an [Amazon FSx (NetApp ONTAP)](https://aws.amazon.com/fsx/netapp-ontap/)
file system using variables in vault, including:
- DNS
- CIFS server with encrypted DC connections to Active Directory
- domain admin user reconciliation (fsxadmin role)
