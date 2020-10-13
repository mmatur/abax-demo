
# Script

- We have a kubernetes platform

## Clean up

```bash
kubectl delete statefulsets.apps -n traefikee  --all
kubectl delete deployments.apps -n traefikee  --all
kubectl delete daemonsets.apps -n traefikee  --all
kubectl delete -n traefikee secrets abax-mtls
kubectl delete -n traefikee secrets abax-tokens
kubectl delete -n traefikee pvc --all
kubectl delete ingressroutes.traefik.containo.us --all --all-namespaces
kubectl delete middlewares.traefik.containo.us --all --all-namespaces
```

## Install TraefikEE

```bash
## GitOps
kubectl create namespace traefikee
kubectl create secret generic abax-license --from-literal=license="${TRAEFIKEE_LICENSE}" -n traefikee
curl -so gitops/00-enterprise.yaml "https://install.enterprise.traefik.io/v2.3?cluster=abax&namespace=traefikee&staticconfig=static.toml"
k apply -f gitops/

kubectl exec -n traefikee abax-controller-0 -- /traefikee generate credentials --kubernetes.kubeconfig="${KUBECONFIG}"  --cluster=abax > config.yaml
teectl cluster import --file="config.yaml" --force

teectl cluster use --name abax
```

## KeyCloak

```bash
kubectl apply -f keycloak/

KEYCLOAK_URL=https://keycloak.abax.containous.tech/auth &&
echo "" &&
echo "Keycloak:                 $KEYCLOAK_URL" &&
echo "Keycloak Admin Console:   $KEYCLOAK_URL/admin" &&
echo "Keycloak Account Console: $KEYCLOAK_URL/realms/traefiklabs/account" &&
echo ""
```
