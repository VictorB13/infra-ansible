# infra-ansible

Configure the EC2 instance as a single-node k3s cluster (Configuration as Code).

## Stack

- Ansible
- k3s
- iptables (host hardening, not ufw)
- fail2ban (SSH brute-force protection)

## Roles

| Role | Purpose |
|---|---|
| `packages` | Install base OS packages (incl. fail2ban) |
| `k3s` | Install and configure single-node k3s server |
| `hardening` | iptables firewall + fail2ban SSH jail |

## Run manually

1. Set the node IP and SSH key:

```bash
export K3S_NODE_IP=<ec2-public-ip>
export SSH_PRIVATE_KEY_PATH=~/.ssh/id_rsa
```

2. Run the full playbook:

```bash
ansible-playbook playbooks/site.yml
```

Or run roles one by one:

```bash
ansible-playbook playbooks/packages.yml
ansible-playbook playbooks/k3s.yml
ansible-playbook playbooks/hardening.yml
```

## Order

`packages` → `k3s` → `hardening`

## Notes

- Inventory: `inventory/hosts.yml` (host comes from `K3S_NODE_IP`)
- SSH user: `ubuntu` (Ubuntu AMI from Terraform)
- This repo is meant to be called from the `infra-terraform` **Build** job after `terraform apply`
