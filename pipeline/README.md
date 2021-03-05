### Installing Argo

Reference: https://github.com/BCDevOps/developer-experience/blob/cailey/artifactory/argo/apps/artifactory/pipeline/README.md

This assumes that the CRDs have already been created (they have been in KLAB and Silver). Argo Workflows should be installed on the namespace scale.

Perform the installion like this in tools namespace: 
`oc process -f install.yaml --param-file=install.param | oc apply -n [TOOLS_NAMESPACE] -f -`

### Argo Workflows

For pipeline development, argo workflows can be manually deployed by port-forwarding to the remote argo server,
```console
oc -n $(oc project --short) port-forward svc/argo-server 2746:2746
```

and running the workflow (this requires installing argo locally).
```console
argo submit -n $(oc project --short) pipeline/workflow.yaml
```
