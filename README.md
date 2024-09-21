Tmdb =  794cee3ccdc629933512fc84db90cd51

## Step 9: Deploy ArgoCD on EKS to fetch the manifest files to the cluster

### 1. Create a namespace argocd

```
kubectl create namespace argocd
```
### 2. Add argocd repo locally

```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml
```

### 3. By default, argocd-server is not publically exposed. In this scenario, we will use a Load Balancer to make it usable:

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

### 4. We get the load balancer hostname using the command below:
 ```
 kubectl get svc argocd-server -n argocd -o json
 ```

 ### 5. We need to enter the Username and Password for ArgoCD. The username will be admin by default. For the password, we need to run the command below:

 ```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
 ```