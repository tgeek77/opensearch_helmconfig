# What is this?

The purpose of these files to to provide an easy way to deploy a simple single-node OpenSearch Cluster + Dashboard.

* opensearch.yaml - This is the values file for the opensearch helm chart.
* opensearch-dashboard.yaml - This is the values file for the dashbaord helm chart.

## opensearch.yaml changes
1. Changed singleNode to true
2. Changed the service `type` from ClusterIP to LoadBalancer and placed the IP of the VM that I am using in the `loadBalancerIP`.
    - Ports 9200 and 9300 which are needed by OpenSearch are now available.

## opensearch-dashboard.yaml
1. Changed the service `type` from ClusterIP to LoadBalancer and placed the IP of the VM that I am using in the `loadBalancerIP`.
    - Port 5601 for the Dashboard is now available.

# Deployment Steps

These are the steps that I am using to deploy OpenSearch and the Dashboard. I am using the latest versions of kubectl and helm on K3s.

## Deploy tools:

### kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod 777 kubectl
sudo mv kubectl /usr/local/bin/
```

### helm
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
rm get_helm.sh
```

## Deploy K3s:
The extra ENV variables with k3s saved the kubeconfig file to the .kube directly in the homedir. K3s will run with its own built in kubectl command without moving this file, but I dislike using root unless absolutely necessary. 

```
mkdir -pv ~/.kube

curl -sfL https://get.k3s.io | \
K3S_KUBECONFIG_OUTPUT="~/.kube/config" \
K3S_KUBECONFIG_MODE="0600" sh -

sudo chown $USER ~/.kube/config
```

## Prepare the OS:
OpenSearch require that the vm.max_map_count is set to 262144. I like rebooting after doing this to make sure that the setting is activated on the next boot.

```
sudo su -c 'echo vm.max_map_count=262144 >> /etc/sysctl.conf'

sudo reboot
```

## Deploy OpenSearch and the Dashboard

### Updated Helm
```
helm repo add opensearch https://opensearch-project.github.io/helm-charts/
helm repo update
```

### Deploy OpenSearch
If you're going to be using the values files that are found in this repo, I suggest replacing the IP addresses that I included with IP addresses for your machine or better yet set up your own LoadBalancer app such as MetalLB to provide an IP address.

```
helm install opensearch opensearch/opensearch \
  --namespace opensearch \
  --create-namespace \
  -f opensearch.yaml  
```  
  
### deploy dashboard
```
helm install opensearch-dashboards opensearch/opensearch-dashboards \
  --namespace opensearch \
  -f opensearch-dashboards.yaml
```

# Results
The result of deploying OpenSearch and the Dashboard is that a simple single-node OpenSearch cluster will be created that can be used for testing purposes without needing to do port forwarding or other ugly temporary hacks.

## What's next?
- Logstash
- Beats
