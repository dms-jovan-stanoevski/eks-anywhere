* Installs ArgoCD as a load balanced service without Ingress (best to keep ArgoCD without Ingress)
* ArgoCD by default creates an internal SSL self-signed certificate
* For the purpose of this example (i.e. managing EKS A clusters) create the git repository upfront
* As seen in this example, we will create a directory inside of the repository named "eksa-clusters". This is also given as a path parameter in the ArgoCD application
* Create a project deployment token within the repository > settings > access tokens. 
* This token will be used as a password along with the username "git" to connect from ArgoCD

```
kubectl create -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.0.4/manifests/install.yaml
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl get svc -n argocd
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
* Login to ArgoCD
* Copy the contents of the Gitlab server. The TLS certificate content was created during the creation of the Gitlab server and will be located in $HOME/eks-anywhere/gitops/gitlab/oidc-https and saved as the gitlab hostname crt file

![image](https://user-images.githubusercontent.com/39495790/178545230-edb25578-4099-4e76-bd1f-dc718fefa026.png)


* Create a git repository to connect to using https


![image](https://user-images.githubusercontent.com/39495790/178546138-5b075f35-1c6c-4e57-aa10-2be676de77a7.png)


The username will be git and the password will be the deploy token. We do not need to pass the TLS certificate again as it is already configured in the listed of trusted certs


![image](https://user-images.githubusercontent.com/39495790/178546394-3cc476a7-3604-4f8b-9899-4815957be635.png)

![image](https://user-images.githubusercontent.com/39495790/178546616-40f4a72e-f2da-4116-944e-cfa7083d0cbb.png)

* Create application from the git repository listing itself

![image](https://user-images.githubusercontent.com/39495790/178548291-a133ee9a-872c-45e3-85f2-f75e4979c7d3.png)

![image](https://user-images.githubusercontent.com/39495790/178548483-709a115f-558e-4541-bfd9-33cfae83d8b6.png)

![image](https://user-images.githubusercontent.com/39495790/178548621-b604617d-3d97-41f3-b5ac-328030da4a74.png)

Once the application is created it will try to sync the resources

![image](https://user-images.githubusercontent.com/39495790/178548996-c55d48d9-1b57-4358-90e2-47b8fbc23d97.png)
