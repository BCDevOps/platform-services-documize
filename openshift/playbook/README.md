## Ansible Playbook for Operation Automation
This is to be used in Argo pipeline.

## Usage
- Ensure you or your service account is logged into your cluster of choice
- Make sure to update `group_vars/all/vars.yml` for specific environment and fill in the values before starting
- currently available playbook are listed down below

## Setup for Artifactory

Here are the detailed steps on setting up objects to use Artifactory for image repository: https://developer.gov.bc.ca/Artifact-Repositories-(Artifactory)


These are done manually for all dev/test/prod namespaces, and should be good once it's setup.

For each namespace:
```shell
# create image pull secret:
oc create secret docker-registry <pull-secret-name> \
    --docker-server=artifacts.developer.gov.bc.ca \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<username>@<namespace>.local

# assign secret to SA:
oc secrets link default artifactory-pull-secret
oc secrets link builder artifactory-pull-secret

# sample on how to use image from Artifactory:
apiVersion: v1
kind: Pod
metadata:
  name: <pod-name>
spec:
  containers:
  - name: <container-name>
    image: artifacts.developer.gov.bc.ca/<repo-name>/<image>:<tag>
  imagePullSecrets:
  - name: artifactory-pull-secret
```

## Run
```shell
# <env>: tools / dev / test / prod
# - create network policies from oc template
ansible-playbook knp.yml -e env=<env>

# - create DB secret from template for correct matching of existing DB data
ansible-playbook db-secrets.yml -e env=<env>

# - create statefulset for patroni DB from oc template
ansible-playbook db-deploy.yml -e env=<env>
```

## To create 301 documize-redirect for migration process
```shell
# - create 301 redirect from oc template
ansible-playbook maintenance-redirect.yml
```
