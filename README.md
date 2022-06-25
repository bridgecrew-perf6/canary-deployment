# Canary - [ Draft ]

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.22.0/deploy/mandatory.yaml
kubectl expose deployment -n ingress-nginx nginx-ingress-controller --port 80 --type LoadBalancer --name ingress-nginx
docker build -t api-version:1.0.0 v1/.
docker save api-version:1.0.0 | (eval $(minikube docker-env) && docker load)
kubectl apply -f v1/api-version-deployment.yaml
kubectl apply -f v1/api-version-service.yaml
kubectl apply -f v1/api-version-ingress.yaml
curl $(minikube service api-version --url)/api/version
```
