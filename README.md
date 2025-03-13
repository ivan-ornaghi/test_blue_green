### Test blue_green ###

- Clone repo.

git clone https://github.com/ivan-ornaghi/test_blue_green.git

- Up cluster with Kind.

kind create cluster --config cluster-kind/cluster_kind.yaml
kind get clusters
kubectl get node

- Install ci-cd argocd

helm install argocd argocd/argocd --namespace argocd

- Create a connection with argo in that case I didn´t use ingress just a service from Kubernetes.

kubectl port-forward svc/argocd-server -n argocd 8080:443

- Take a passaword from argocd user is "admin"

kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" |base64 -d && echo

- Create repo in argocd, but need encode base64 these fields in secret.

> example: echo "repo.com" | base64 encode
  Put those values in the fields below.

  - url: ""
  - password: ""
  - username: ""

kubectl apply -f argocd/applications-argocd-blue-green/secret-repo.yaml

- Create a application in argocd.

kubectl apply -f argocd/applications-argocd-blue-green/application-argocd-blue-green.yaml

- After of the created application in argocd look in dashboard and confirm if an application on dashboard argocd.

- Create a connection with application.

kubectl port-forward svc/my-app -n default 9000:9000

## In the browser, localhost:9000/ will probably appear in the blue version. ##

- > After the application is deployed and sync with argocd, need just one step manually.
- > Click in the application on argocd -> details -> parameters -> edit -> values files (Change to values-green.yaml) and save.
- > Now argocd shows that the application must be OutOfSync, So click in sync again.
- > See in cluster kubernetes wiht this commands below:

- kubectl get pod -n default 
- kubectl get svc -n default
- kubectl describe svc my-app -n default -> probably need some endpoints.
- kubectl get pod -n default -o wide -> see IP of the pod, probably pod name is green.

## Now put in browser localhost:9000/

- > If the browser be in the cache please follow this command below:

- lsof -i :9000
- kill -9 the process and run command below again.
- kubectl port-forward svc/my-app -n default 9000:9000

## Reference

- > https://kind.sigs.k8s.io
- > https://kubernetes.io/docs/concepts/