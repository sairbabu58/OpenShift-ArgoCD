# How to access Openshift ArgoCD from Externally

-> ArgoCD is installed on OCP Cluster and operator based 
-> This argoCD will act as a centerlized ArgoCD Instance for the application or Infra operation

# Process to Do it.
-> prerequisite
-> One external IP
-> DNS name for that external IP
-> MetalLB configuration


-> Install ArgoCD operator 
-> Create ArgoCD Instance or (Use Default Instance)
-> In this case we are created a new Instance for the application Argocd. Each project having there own argocd 

-> Create ArgoCD instance on app-x-argocd-project

**IMP: modify the existing configuration.**

```
route:
 enabled: false   
service:
  type: LoadBalancer
host: argocd-rs-dev.ocp.example.com
```
```
-> Create a new SVC as a LoadBalancer
-> oc get svc/argocd-server -oyaml > external-svc.yaml
```
```
 kind: Service
 metadata:
annotations:
service.alpha.openshift.io/serving-cert-signed-by: openshift-service-serving-signer@1740641974
service.beta.openshift.io/serving-cert-secret-name: argocd-server-tls
service.beta.openshift.io/serving-cert-signed-by: openshift-service-serving-signer@1740641974
 metallb.universe.tf/address-pool: otps-sm-argocd-ipaddresspool ##optional
labels:
app.kubernetes.io/component: server
app.kubernetes.io/managed-by: argocd
 app.kubernetes.io/name: argocd-server
app.kubernetes.io/part-of: argocd
name: argocd-server-external
namespace: dev-rs-argocd
spec:
internalTrafficPolicy: Cluster
ipFamilies:
- IPv4
ipFamilyPolicy: SingleStack
ports:
- name: http
port: 80
 protocol: TCP
 targetPort: 8080
- name: https
 port: 443
protocol: TCP
targetPort: 8080
selector:
 app.kubernetes.io/name: argocd-server
sessionAffinity: None
type: LoadBalancer
loadBalancerIP: 10.0.0.100  ## optional
```

# Configure IPaddresspool and L2Adv

```

            - complianceType: musthave
              objectDefinition:
                apiVersion: metallb.io/v1beta1
                kind: IPAddressPool
                metadata:
                  name: argocd-ipaddresspool
                  namespace: metallb-system
                spec:
                  addresses:
                    - 10.0.0.100/24
                  autoAssign: true
                  avoidBuggyIPs: true
                  serviceAllocation:
                    namespaces:
                      - app-x-argocd
                    serviceSelectors:
                      - matchExpressions:
                          - key: app.kubernetes.io/name
                            operator: In
                            values:
                              - argocd-server

            - complianceType: musthave
              objectDefinition:
                apiVersion: metallb.io/v1beta1
                kind: L2Advertisement
                metadata:
                  name: argocd-l2advertisement
                  namespace: metallb-system
                spec:
                  ipAddressPools:
                    - argocd-ipaddresspool
```

 **Access argocd using external DNS-name**
