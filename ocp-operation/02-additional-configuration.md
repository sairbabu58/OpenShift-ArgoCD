# Deploying resources to a different namespace
```
$  oc label namespace <target_namespace> argocd.argoproj.io/managed-by=<argocd_namespace>
$ oc label namespace spring-petclinic argocd.argoproj.io/managed-by=openshift-gitops 
$ oc label namespace spring-petclinic argocd.argoproj.io/managed-by=test-argocd
```
**<target_namespace>
Specifies the name of the namespace you want Argo CD to manage.**

**<argocd_namespace>
Specifies the name of the namespace where Argo CD is installed.**






