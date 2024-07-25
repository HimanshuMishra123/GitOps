   - **ArgoCD**:
   - use https://operatorhub.io/operator/argocd-operator to deploy argocd on your kubernetes which will be deployed in 'operators' namespace. For controllers prefer operator way to install as future is this.
   - Argo cd will watch for updates on manifest repo and Deploys the updated manifests from the Git repository to the Kubernetes cluster.

Example ArgoCD Configuration:
#argocd.yml
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
spec:
  source:
    repoURL: 'https://github.com/your-org/your-repo'
    path: 'manifests'
    targetRevision: HEAD
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
  project: default
```

(or)
#argocd-basic.yml (and mention repo and target on argo cd UI)
```yaml
apiVersion: argoproj.io/v1alpha1
kind: ArgoCD
metadata:
  name: example-argocd
spec: {}
```
`kubectl apply -f argocd-basic.yml`  creating a new ArgoCD resource in the namespace(operators) where the operator is installed.
`kubectl get pods -n operators`

To access argo cd UI on browser we have change the argocd service cluster IP to node port mode. 
`kubectl get svc` copy argocd server service name, here it is 'example-argocd-server'
`kubectl edit svc example-argocd-server` change type from ClusterIP to NodePort
`kubectl get svc` you will see argocd server service is changed to node port mode


if you are on minikube do `minikube service example-argocd-server` minikube will do somme port mapping and generate a url using which you can access your argocd service on browser.

Argocd Username: Admin
Password: argocd stores default password inside  secret resources name argocd cluster.
`kubectl get secret`
`kubectl edit secret example-argocd-cluster` copy admin password which is base64 encoded and :q!
`echo <encoded_password> | base64 -d` copy the password and ignore the % sign in last do not copy that
Login to Argocd>> create application>> Application Name>> Project name :default>> sync: automatic>> Repo URL>> Revision:Branch:head>> Path: where your manifest are>>cluster url :select same from dropdown >> Namespace: default>>create>> application will be automatically deployed on k8s cluster.