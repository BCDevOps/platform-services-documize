# Backup database and verify restore

We have recently switch to use Backup-Container Helm Chart for database backup and restore task.

Please refer to the resources repo for details:
- Backup-Container: https://github.com/BCDevOps/backup-container
- Helm Chart: https://github.com/bcgov/helm-charts/tree/master/charts/backup-storage

1. Build image

Use the original backup container oc templates here: https://github.com/BCDevOps/backup-container/blob/master/openshift/templates/backup

```shell
# build in tools namespace
oc project <namespace>_tools

# build using the json template
curl https://raw.githubusercontent.com/BCDevOps/backup-container/master/openshift/templates/backup/backup-build.yaml | oc process -f - | oc apply -f -

# once the image is build, make sure to tag it for each environment
oc tag <is_name>:latest <is_name>:<env>
```

2. Run with helm chart

Here are the details about the chart we are using: https://github.com/bcgov/helm-charts/tree/master/charts/backup-storage

```shell
# switch to deployment namespace
oc project <namespace>_<env>

# import image from tools
oc tag <namespace>_tools/<is_name>:<env> <is_name>:<env>

# first update the configuration:
cp sample-config.yaml tmp-config-<env>.yaml
# fill in the config file!

# add repo:
helm repo add bcgov http://bcgov.github.io/helm-charts
helm repo update

# install
helm upgrade --install patroni-backup-storage -f ./tmp-config-<env>.yaml bcgov/backup-storage

# you will get a helm release with backup container deployed

# to delete the backup container
helm uninstall patroni-backup-storage
```

3. Run backup and verify

```shell
# rsh into backup pod

# view config:
./backup.sh -c

# list all backup:
./backup.sh -l

# run backup:
./backup.sh -1

# verify:
./backup.sh -v all
```

4. See notification on RocketChat channel

5. If you need to restore DB from a backup

```shell
# manual copy over the DB dump from source to target backup pod:
oc cp <source_pod>:<db_dump_file_path> ./
oc cp ./<db_dump_file> <target_pod>:<db_dump_file_path>

# scale down app
oc scale dc <documize_dc> --replicas=0

# rsh into backup pod

# list all backup to verify the dump file exist, and get the path:
./backup.sh -l

# restore with specific db dump file:
./backup.sh -r <db_service_name>:5432/<db_name> -f <db_dump_file_path>

# then scale app up and verify
```
