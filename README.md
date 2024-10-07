
k3d cluster create -p "32080:80@loadbalancer" -p "32443:443@loadbalancer"
k3d cluster create kube-cluster \
  --agents 3 \
  --k3s-node-label topology.kubernetes.io/zone=zone-a@agent:0 \
  --k3s-node-label topology.kubernetes.io/zone=zone-b@agent:1 \
  --k3s-node-label topology.kubernetes.io/zone=zone-c@agent:2

or 
k3d cluster create --config k3d_config_calico.yaml

curl -O https://k3d.io/v5.6.3/usage/advanced/calico.yaml
mkdir -p ~/local/storage/k3d

```
helm install ingress-nginx ingress-nginx/ingress-nginx --version=4.11.2 --namespace ingress-nginx --create-namespace 
```
or 
```
helm install haproxy-kubernetes-ingress haproxytech/kubernetes-ingress -n haproxy-controller -f values.yaml
```

```
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.14.0 \
  --set installCRDs=true
```

```
kubectl create secret tls mkcert-ca-key-pair \
--key "$(mkcert -CAROOT)"/rootCA-key.pem \
--cert "$(mkcert -CAROOT)"/rootCA.pem -n cert-manager
```

```
kubectl apply -f - <<EOF
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: mkcert-issuer
  namespace: cert-manager
spec:
  ca:
    secretName: mkcert-ca-key-pair
EOF
```

## Monitoring

helm install loki grafana/loki-stack -n monitoring -f loki-config.yml

kubectl get secret loki-grafana -n monitoring -o jsonpath={.data.admin-user} | base64 -d; echo
kubectl get secret loki-grafana -n monitoring -o jsonpath={.data.admin-password} | base64 -d; echo

## Gitlab Runner

kubectl create namespace gitlab
helm install --namespace gitlab gitlab-runner gitlab/gitlab-runner \
  --set rbac.create=true \
  --set runners.privileged=true \
  --set gitlabUrl=https://gitlab.com/ \
  --set runnerRegistrationToken=...

