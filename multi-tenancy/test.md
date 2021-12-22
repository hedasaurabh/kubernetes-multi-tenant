```
$ kubectl create ns atlan-saas
namespace/atlan-saas created
```

```
$ ./kubectl-hns create customer-abc -n atlan-saas
Successfully created "customer-abc" subnamespace anchor in "atlan-saas" namespace
```

```
$ ./kubectl-hns create customer-xyz -n atlan-saas
Successfully created "customer-xyz" subnamespace anchor in "atlan-saas" namespace
```

```
$ ./kubectl-hns tree atlan-saas
atlan-saas
├── [s] customer-abc
└── [s] customer-xyz

[s] indicates subnamespaces
```

```
$ helm repo add grafana https://grafana.github.io/helm-charts
"grafana" has been added to your repositories
```

```
saurabh@Saurabh-Ubuntu:~$ helm install -n customer-abc grafana-abc grafana/grafana
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
