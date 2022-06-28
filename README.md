# Canary Deployment K8s

## Prepara o pacote v1.0.0 e envia para Minikube

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.22.0/deploy/mandatory.yaml
kubectl expose deployment -n ingress-nginx nginx-ingress-controller --port 80 --type LoadBalancer --name ingress-nginx
docker build -t api-version:1.0.0 v1/.
docker save api-version:1.0.0 | (eval $(minikube docker-env) && docker load)
```

#### Configurando o deployment, service e ingress - v1.0.0

```bash
kubectl apply -f v1/api-version-deployment.yaml -f v1/api-version-service.yaml -f v1/api-version-ingress.yaml
```

#### Resultado /api/version - v1.0.0

```bash
curl $(minikube service api-version --url)/api/version
```

```json
{"id":"1.0.0", "name":"Version One"}
```

## Prepara o pacote v2.0.0 e envia para Minikube

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.22.0/deploy/mandatory.yaml
kubectl expose deployment -n ingress-nginx nginx-ingress-controller --port 80 --type LoadBalancer --name ingress-nginx
docker build -t api-version:2.0.0 v2/.
docker save api-version:2.0.0 | (eval $(minikube docker-env) && docker load)
```

#### Configurando o deployment, service e ingress 10% - v2.0.0

```bash
kubectl apply -f v2/api-version-deployment.yaml

# Create a canary ingress in order to split traffic: 90% to v1, 10% to v2
kubectl apply -f api-version-canary-v2.yaml

# Deletar canary
$ kubectl delete -f ./ingress-v2-canary.yaml

# Then finish the rollout, set 100% traffic to version 2
$ kubectl apply -f ./ingress-v2.yaml
```