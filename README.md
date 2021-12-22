# kubernetes-multi-tenant

## 1. Description

Need: We need a cloud-native solution where we are planning to run the Atlan's SAAS offering on Kubernetes platform. As this is a SAAS platform the pain of deploying, managing and administrating the platform is Atlan's Responsibility. Also this platform would host multiple customer's SAAS deployment of Atlan's SAAS solution so we will need a Multi-tenant Kubernetes Cluster 

Default Kubernetes namespaces are not flexible enough to meet a use case where particular namespace can provide further isolation within it. Also K8s namespaces cannot share resources among namespaces belonging to the same tenant. Also it won't be a viable solution to deploy dedicated cluster for each customer. With growing customer's, number of cluster would grow which will result in an operational nightmare described as _clusters sprawl_.

## Proposed Solution:

We can use hierarchical namespace to acheive multi tenancy in K8s, hierarchical namespace are a regular Kubernetes namespace that contains a small custom resource that identifies a single, optional, parent namespace. 
As customers would like to use SAAS platform instead of deploying it in their cloud, we can use hierarchical namespaces where each parent namespace would be the isolation layer. Each parent namespace would denote a particular tenant and the tenant admin would have the RBAC role associated with it to create certain sub-namespaces. 

This sub namespace would hold the SAAS platform for each customer. There would be multiple sub namespaces in the parent namespace. All the workload for SAAS platform for one customer would run in its own namespace.

__Multi-tenancy__ in a Kubernetes cluster stands  for a point of isolation where different namespaces have a whole mini-ecosystem of apps running in a namespace which are completely isolated from a different namespace.

For each customer there would a sub-namespace which will be independent of other namespaces. Based on the requirements Atlan team would need to define:
1. Resource quota in terms of the compute, storage and network resources (network - optional) for the namespace.
2. Names of the admins and account manager that would be accessing the sub-namespaces.

## 2. Technology

### Chosen technology/tools

* Hierarchical Namespace Controller
* Kubectl HNC plugin

### Reason for technology decision

To extend the functionality of Kubernetes in general and Kubernetes Namespaces in particular to add additional isolation within a namespace we need Hierarchical Namespace Controller.
This project supports this functionality and is backed by Kubernetes community. This project also has a good chance to merge in the core Kubernetes platform in the future.


Hierarchy within the Atlan prod cluster would look like this

```
atlan-saas Namespace
  -> customer-abc Namespace
	  -> SAAS DEV NS
	  -> SAAS TEST NS

  -> customer-xyz Namespace
	  -> SAAS DEV NS
	  -> SAASTEST NS
``` 
## 3. Component description


HNC - Hierarchical namespaces make it easier to share your cluster by making namespaces more powerful. For example, you can create additional namespaces under your team's namespace, even if you don't have cluster-level permission to create namespaces.
Then easily apply policies like RBAC and Network Policies across all namespaces in your team (e.g. a set of related microservices).

More details are [here](https://github.com/kubernetes-sigs/hierarchical-namespaces/blob/master/docs/user-guide/concepts.md)


## 4. Alternate Approach

There is project called capsule (github.com/clastix/capsule) which also implements multi tenancy in Kubernetes cluster. Capsule Controller in a single cluster, aggregates multiple namespaces in Tenant. Capsule Engine will keep the different tenants isolated from each other. Network policies, RBAC & Resource Quotas defined at tenant level are automatically inherited by all the namespaces in the tenant.


## 5. Installation

Install HNC using below command:
1. Clone this repository.
2. export KUBECONFIG= <path_of_kubeconfig_file>
3. cd kubernetes-multi-tenant
4. kubectl apply -f hnc-install/hnc-manager.yaml

Install the kubectl HNC plugin
```
HNC_VERSION=v0.9.0
HNC_PLATFORM=linux_amd64 # also supported: darwin_amd64, windows_amd64
curl -L https://github.com/kubernetes-sigs/hierarchical-namespaces/releases/download/${HNC_VERSION}/kubectl-hns_${HNC_PLATFORM} -o ./kubectl-hns
chmod +x ./kubectl-hns
```

## 5. Hierarchical Configuration

All the commands are provided inside multi-tenancy directory in test.md file
