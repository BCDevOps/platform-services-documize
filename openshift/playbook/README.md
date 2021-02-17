## Ansible Playbook for Operation Automation
This is to be used in Argo pipeline.

## Usage
- Ensure you or your service account is logged into your cluster of choice
- Make sure to update `group_vars/all/vars.yml` for specific environment and fill in the values before starting
- currently available playbook:
  - create DB secret from template for correct matching of existing DB data


## Run
```shell
# <env>: tools / dev / test / prod
ansible-playbook create-secret-from-template.yml -e env=<env>
```
