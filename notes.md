## jenkins
helm install my-jenkins oci://registry-1.docker.io/bitnamicharts/jenkins
  
export SERVICE_IP=$(kubectl get svc --namespace default my-jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
echo "Jenkins URL: http://$SERVICE_IP/"

echo Username: user
echo Password: $(kubectl get secret --namespace default my-jenkins -o jsonpath="{.data.jenkins-password}" | base64 -d)

---

## Argo CD

kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl get pods -n argocd

