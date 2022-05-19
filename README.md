# Azure Functions Demo
Resources for Azure Functions Demo video

# Install Chocolatey & HELM

Chocolatey install page:

https://chocolatey.org/install

Helm website:

https://helm.sh/

Helm install commands: 

    choco install kubernetes-helm
    helm version

# Install Azure Functions Core Tools

Work with Azure Functions Core Tools:

https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local

Verify commands: 

    func version
    func -help

# Install Visual Studio Code

Homepage

https://code.visualstudio.com/

# Azure Function creation

Create function project

    func init AzureFunctionsDemo --docker --worker-runtime dotnet --language csharp
    cd AzureFunctionsDemo

Create http trigger function

    func new --template "Http Trigger" --name VoteForMe

Start function locally

    func start --build --verbose

Create docker image for project

    docker build -t voting-http-demo:v1 .

# Install KEDA

keda helm chart

    helm repo add kedacore https://kedacore.github.io/charts
    helm repo update

keda namespace in k8s

    kubectl create namespace keda

keda install in k8s

    helm install keda kedacore/keda --namespace keda

# Azure Function deployment

Create kubernetes manifest file

    func kubernetes deploy --name k8s-voting-http-demo --image-name "voting-http-demo:v1" --dry-run > k8s-voting-http-demo.yml

Deploy manifest file

    kubectl apply -f k8s-voting-http-demo.yml

Verify deployment

    kubectl get deployments
    kubectl get services 
    kubectl get pods

    kubectl logs <pod-name>

Create port forward to the service

    kubectl port-forward service/k8s-voting-http-demo 17000:80

Test port forward
    curl --get http://127.0.0.1:17000/api/VoteForMe?name=amber -v

# Nginx Ingress Controller Setup

kubernetes docs ingress:

https://kubernetes.io/docs/concepts/services-networking/ingress/

Nginx ingress install

    helm repo add nginx-stable https://helm.nginx.com/stable
    helm repo update

Create k8s namespace

    kubectl create namespace ingress-nginx

Install helm

    helm install ingress-controller nginx-stable/nginx-ingress --namespace ingress-nginx

Verify install
    kubectl get service -n ingress-nginx

Host entry

    127.0.0.1 voting.azf.local

ingress.yml contents

https://gist.github.com/chrisxpr/cc125def93c4e9eca42b226af9dffb96

Apply ingress rule

    kubectl apply -f ingress.yml

Check ingress rule

    kubectl describe ingress voting-http-ingresss -n ingress-nginx

View ingress logs

    kubectl get pods -n ingress-nginx
    kubectl logs <pod-name> -n ingress-nginx 

# Endpoint Testing

    curl --get http://voting.azf.local/api/VoteForMe?name=fred -v
