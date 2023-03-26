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