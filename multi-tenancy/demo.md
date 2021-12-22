## Create a parent/org namespace

```
$ kubectl create ns atlan-saas
namespace/atlan-saas created
```

## Create a subnamespace for customer in atlas-saas's namespace.
```
$ ./kubectl-hns create customer-abc -n atlan-saas
Successfully created "customer-abc" subnamespace anchor in "atlan-saas" namespace
```

## Create a subnamespace for customer in atlas-saas's namespace.
```
$ ./kubectl-hns create customer-xyz -n atlan-saas
Successfully created "customer-xyz" subnamespace anchor in "atlan-saas" namespace
```

## Using below command check the relationship between the namespaces
```
$ ./kubectl-hns describe atlan-saas
Hierarchy configuration for namespace atlan-saas
  No parent
  Children:
  - customer-abc (subnamespace)
  - customer-xyz (subnamespace)
  No conditions
```
```
$ ./kubectl-hns tree atlan-saas
atlan-saas
├── [s] customer-abc
└── [s] customer-xyz

[s] indicates subnamespaces
```

## Use helm to install grafana in the specific namespace
```
$ helm repo add grafana https://grafana.github.io/helm-charts
"grafana" has been added to your repositories
```

```
$ helm install -n customer-abc grafana-abc grafana/grafana
NAME: grafana-abc
LAST DEPLOYED: Thu Dec 23 02:52:50 2021
NAMESPACE: customer-abc
STATUS: deployed
REVISION: 1

```

```
$ helm install -n customer-xyz grafana-xyz grafana/grafana
NAME: grafana-xyz
LAST DEPLOYED: Thu Dec 23 02:57:09 2021
NAMESPACE: customer-xyz
STATUS: deployed
REVISION: 1
```

## Demonstrate the power of hierarchial namespace

Let's say that we want to make Site Reliability Engineer (SRE) for customer-abc & customer-xyz, so we can create an RBAC Role and a RoleBinding for the parent namespace which is `atlan-saas`. This role and rolebinding will be propagated to the child namespace (customer-abc & customer-xyz).

Create a role `org-sre` in `atlan-saas` namespace
```
$ kubectl -n atlan-saas create role org-sre --verb=update --resource=deployments
role.rbac.authorization.k8s.io/org-sre created
```

Create a rolebinding org-sres in `atlan-saas` namespace
```
$ kubectl create -n atlan-saas rolebinding org-sres --role org-sre --serviceaccount=atlan-saas:default
rolebinding.rbac.authorization.k8s.io/org-sres created
```

As we have HNC running on the cluster, from the below output we can see that the role & rolebinding getting propagated to the child namespaces

```
$ kubectl get role,rolebinding -n customer-abc
NAME                                          CREATED AT
role.rbac.authorization.k8s.io/org-sre        2021-12-22T22:05:57Z

NAME                                                 ROLE                AGE
rolebinding.rbac.authorization.k8s.io/org-sres       Role/org-sre        33s
```

```
$ kubectl get role,rolebinding -n customer-xyz
NAME                                              CREATED AT
role.rbac.authorization.k8s.io/org-sre            2021-12-22T22:05:57Z

NAME                                                     ROLE                    AGE
rolebinding.rbac.authorization.k8s.io/org-sres           Role/org-sre            4m25s
```
