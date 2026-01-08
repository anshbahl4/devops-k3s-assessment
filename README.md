1- Docker install

curl -fsSL https://get.docker.com | sudo sh
sudo usermod -aG docker ansh

2- k3s install
curl -sfL https://get.k3s.io | sh -

3-kubectl config
mkdir -p ~/.kube
cp /etc/rancher/k3s/k3s.yaml ~/.kube/config

4- Check:
docker version
kubectl get nodes
kubectl get pods -A

Part 2 – Deploy Open WebUI
helm repo add open-webui https://helm.openwebui.com/
helm repo update
kubectl create namespace openwebui
helm install webui open-webui/open-webui -n openwebui --set service.type=ClusterIP
kubectl get all -n openwebui

Part 3 – OIDC

values-oidc.yaml:

oidc:
  clientId: "test"
  clientSecret: ""
  issuer: "https://<ansh.com>/auth/realms/hyperplane/.well-known/openid-configuration"
  scopes:
	- openid
	- profile
	- email

Apply:

helm upgrade webui open-webui/open-webui -n openwebui --values values-oidc.yaml

Part 4 – Debugging (Short)

The app did not fail because the Helm chart did not pass OIDC values to the container.
I checked logs and environment variables, and there were no OIDC env vars, so OIDC was ignored.

Commands checked:

helm get values webui
kubectl logs open-webui-0 -n openwebui
