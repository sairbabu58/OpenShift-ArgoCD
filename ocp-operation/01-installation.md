# ArgoCD Admin operation Instance
# ArgoCD Application operation Instance



#  Start ArgoCD Admin operation Instance

1) Go to the operator hub and install the gitops operator 
2) Default ArgoCD instance will get create on openshift-gitops NS
3) Once installation is done, login to ocp web-UI > right side top > click the 9 dots (...) > Cluster ArgoCD.
```
NS: 
 - openshift-gitops-operator
 - openshift-gitops
```

## Login to ArgoCD webUI:

1) Login to ArgoCD webUI using openshift credentials
```
  $ oc get route -n openshift-gitops (or) 
  $ login to ocp web-UI > right side top > click the 9 dots (...) > Cluster ArgoCD
```
3) Authentication:
```
  - Use openshift integrated identity 
  - Or use ArgoCD default username and password:
$ oc -n openshift-gitops extract secret/openshift-gitops-cluster --to=-
```


## Configure the secure access ArgoVD WebUI
```
$ oc project openshift-gitops
$ oc get argocd
$ oc edit argocd/openshift-gitops

remove the ownerRef 
  

line 118. add/update the new line
server:
   route:
      enabled: true
      tls:
        termination: reencrypt
```

**again login to argocd webui and check whether login is secure.**


# Grant administrator rights to the ocpadmins group.
```
$ oc edit argocd/openshift-argocd

  rbac:
    policy: |
      g, ocpadmins, role:admin
    scopes: '[groups]'
```


# Configure the default instance to trust the cluster certificate authority. Argo CD accesses only trusted repositories.
```
$ oc create configmap -n openshift-gitops cluster-root-ca-bundle
$ oc label configmap -n openshift-gitops cluster-root-ca-bundle config.openshift.io/inject-trusted-cabundle=true
$ oc edit argocd -n openshift-gitops openshift-gitops

repo: 
   resources:
     ''''
    volumeMounts:
    - mountPath: /etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem
      name: cluster-root-ca-bundle
      subPath: ca-bundle.crt
    volumes:
    - configMap:
        name: cluster-root-ca-bundle
      name: cluster-root-ca-bundle
```






# Start ArgoCD Application operation Instance

By default, the Red Hat OpenShift GitOps operator creates an Argo CD instance in the openshift-gitops namespace. 
This Argo CD instance has permissions to manage cluster-wide resources. Thus, Red Hat recommends using this instance only for 
administration purposes, and to create other, less privileged instances for regular application deployment.

To deploy regular applications, you can deploy new Argo CD instances. By default, the new Argo CD instances have permissions to manage 
resources only in the namespace where they are deployed.

You can allow an Argo CD instance to manage resources in other namespaces than where it is deployed by adding the 
"argocd.argoproj.io/managed-by" label to the namespace. The following example allows the Argo CD instance in the argocd-ns namespace to 
manage the resources in the managed-ns namespace.
```
Example: 
-> argocd-ns (ArgoCD instance is running)
-> managed-ns-01 , managed-ns-02 (Application NS, which will be manage by argocd-ns)

$ oc label namespace managed-ns argocd.argoproj.io/managed-by=argocd-ns
```


# Common Issues Service account permission Deny






https://access.redhat.com/solutions/7007638
```
$ oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n <namespace> #where you going to deploy your application.

or

$ $ oc label namespace <namespace> "argocd.argoproj.io/managed-by=openshift-gitops"
```

