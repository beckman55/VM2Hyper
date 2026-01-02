# Ansible Directory

- Store playbooks, roles, inventories here.
- Sensitive vars should be encrypted using Ansible Vault.
- Do NOT commit plaintext secrets.

Example:
  ansible-vault create group_vars/all/vault.yml

Recommended:
  Store real secrets in Bitwarden and generate vault files from them.
