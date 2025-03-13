### Test blue_green ###

## Clone repo ##

git clone https://github.com/ivan-ornaghi/test_blue_green.git

## Up cluster with Kind ##

kind create cluster --config cluster-kind/cluster_kind.yaml

## Install ci-cd argocd ##

helm install argocd argocd/argocd --create-namespace argocd --namespace argocd

## Create a connection with argo in that case I didn´t use ingress just a service from Kubernetes. ##

kubectl port-forward svc/argocd-server -n argocd 8080:443

## Take a passaword from argocd user is "admin" ##

kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" |base64 -d && echo

## Create repo in argocd. ##

kubectl apply -f argocd/applications-argocd-blue-green/secret-repo.yaml

## Create a application in argocd. ## 

kubectl apply -f argocd/applications-argocd-blue-green/application-argocd-blue-green.yaml

## After of the created application in argocd look in dashboard and confirm if an application on dashboard argocd. ##

## Create a connection with application. ##

kubectl port-forward svc/my-app -n default 9000:9000

## In the browser, localhost:9000 will probably appear in the blue version. ##

