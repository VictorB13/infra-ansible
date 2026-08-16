# infra-ansible

Configure the EC2 instance as a single-node k3s cluster (Configuration as Code).

## Stack

- Ansible
- k3s
- iptables (host hardening, not ufw)
- fail2ban (SSH brute-force protection)
- ArgoCD (GitOps)

## Roles

| Role | Purpose |
|---|---|
| `packages` | Install base OS packages (incl. fail2ban) |
| `k3s` | Install and configure single-node k3s server |
| `hardening` | iptables firewall + fail2ban SSH jail |
| `argocd` | Install ArgoCD + apply `infra-gitops` root Application |

## How it runs

**Only via GitHub Actions** — the `infra-terraform` **Build** workflow (manual trigger from the GitHub UI):

1. Terraform apply → EC2 created  
2. Workflow writes an inventory file with the instance public IP (`/tmp/k3s-inventory.yml`)  
3. Runs `ansible-playbook playbooks/site.yml -i /tmp/k3s-inventory.yml`  

Order: `packages` → `k3s` → `hardening` → `argocd`

There is no supported local/manual Ansible run path.

## Notes

- Host inventory is generated in CI at `/tmp/k3s-inventory.yml` (not in this repo)  
- SSH user: `ubuntu`  
- SSH key comes from `infra-terraform` secrets (`SSH_PRIVATE_KEY`)  
- ArgoCD admin password is stored in AWS SSM by the Build job (`/todo-app/argocd_admin_password`)
- ArgoCD UI: Ingress + HTTPS at `https://argocd.<ec2-ip>.sslip.io` (also `/todo-app/argocd_url` in SSM)
