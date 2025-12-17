
# If Argocd webconsole secure/insecure issues:
```
$ oc create cm cluster-root-ca-bundle -n openshift-gitops
$ oc label cm/cluster-root-ca-bundle -n openshift-gitops config.openshift.io/inject-trustd-cabundle=true
```


```
- Adding this annotations, It will wait for above resources(NS, Opperator, App) nee to be create before apply this activity.
  annotations:
    argocd.argoproj.io/sync-options: SkipDryRunOnMissingResource=true
    argocd.argoproj.io/sync-wave: "1"
```


**- Note: Dont apply the whole changes and dont over write the whole configuration file. Just apply the additional changes**
```
annotations:
    argocd.argoproj.io/sync-options: ServerSideApply=true,Validate=false
```
```
apiVersion: operator.openshift.io/v1
kind: Console
metadata:
  name: cluster
  annotations:
    argocd.argoproj.io/sync-options: ServerSideApply=true,Validate=false
spec:
  customization:
    customProductName: Production
```
# How to validate ArgoCD and Git-repo connection with secure 

```
$ oc project argocd-ns
$ oc rsh po/argocd-repo-pod curl https://git.example.com:443
- > It should print the output without SSL centificate error 
```
