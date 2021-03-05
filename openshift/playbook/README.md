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
ansible-playbook knps.yml -e env=<env>

# - create DB secret from template for correct matching of existing DB data
ansible-playbook db-secrets.yml -e env=<env>

# - create statefulset for patroni DB from oc template
ansible-playbook db-deploy.yml -e env=<env>
```

## To create 301 documize-redirect for migration process
```shell
# example for prod:
# export NAMESPACE=hmg6pw-prod \n
# export SOURCE_HOST_NAME=docs.pathfinder.gov.bc.ca \n
# export DESTINATION_HOST_NAME=docs.developer.gov.bc.ca \n
ansible-playbook maintenance-redirect.yml -e NAMESPACE=${NAMESPACE} \
    -e SOURCE_HOST_NAME=${SOURCE_HOST_NAME} -e DESTINATION_HOST_NAME=${DESTINATION_HOST_NAME}
```
