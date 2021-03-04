## Ansible Playbook for Operation Automation
This is to be used in Argo pipeline.

## Usage
- Ensure you or your service account is logged into your cluster of choice
- Make sure to update `group_vars/all/vars.yml` for specific environment and fill in the values before starting
- currently available playbook are listed down below

## Run
```shell
# <env>: tools / dev / test / prod
# - create network policies from oc template
ansible-playbook knp.yml -e env=<env>

# - create DB secret from template for correct matching of existing DB data
ansible-playbook db-secrets.yml -e env=<env>

# - create statefulset for patroni DB from oc template
ansible-playbook db-deploy.yml -e env=<env>

# - cross-cluster DB data migration
ansible-playbook db_state_sync.yml -e env=<env> -e source_oc_account=ShellyXueHan -e target_oc_account=shellyxuehan@github
# NOTE: Run the following manual backup/restore steps until we have it automated:
# - Backup dump in ocp3:
oc rsh <backup_pod>
> ./backup.sh -1
> exit

# - Backup restore in ocp4:
oc rsh <backup_pod>
# -- find the target backup file:
> ./backup.sh -l
# -- find the db connection string:
> ./backup.sh -c
# -- run restore:
> ./backup.sh -r documize-patroni-master-dev:5432/documize

# NOTE: you will need to find the super user secret from db secret!
```
