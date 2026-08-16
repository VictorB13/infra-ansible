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

## How it runs

**Only via GitHub Actions** — the `infra-terraform` **Build** workflow (manual trigger from the GitHub UI):

1. Terraform apply → EC2 created  
2. Workflow writes an inventory file with the instance public IP (`/tmp/k3s-inventory.yml`)  
3. Runs `ansible-playbook playbooks/site.yml -i /tmp/k3s-inventory.yml`  

Order: `packages` → `k3s` → `hardening`

There is no supported local/manual Ansible run path.

## Notes

- Host inventory is generated in CI at `/tmp/k3s-inventory.yml` (not in this repo)  
- SSH user: `ubuntu`  
- SSH key comes from `infra-terraform` secrets (`SSH_PRIVATE_KEY`)
