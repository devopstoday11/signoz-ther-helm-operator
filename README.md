# signoz-ther-helm-operator

- Init Signoz Operator

```
$ mkdir signoz-ther-helm-operator
$ cd signoz-ther-helm-operator
$ operator-sdk init --plugins helm --domain signoz.io --group monitor --version v1 --kind Signoz
```

- Clone Signoz Repo to another directory

```
$ git clone https://github.com/SigNoz/signoz.git && cd signoz
$ helm dependency update deploy/kubernetes/platform
```

- Copy Charts to Operator Charts dir and decompress file

```
$ ls
operator signoz-ther-helm-operator
$ cp -r signoz/deploy/kubernetes/platform/charts signoz-ther-helm-operator/helm-charts/signoz/
$ cp -r signoz/deploy/kubernetes/platform/signoz-charts signoz-ther-helm-operator/helm-charts/signoz/ 
$ cd signoz-ther-helm-operator/helm-charts/signoz/charts/
$ tar -xvf *.tgz
$ rm -rf *.tgz
```

- Create `requirements.yaml` file for dependencies add dependencies

```
dependencies:
- name: kafka
  version: 12.0.0
  repository: https://charts.bitnami.com/bitnami
- name: zookeeper
  version: 6.0.0
  repository: https://charts.bitnami.com/bitnami
- name: druid
  version: 0.2.18
  repository: https://charts.helm.sh/incubator
- name: flattener-processor
  repository: "file://./signoz-charts/flattener-processor"
  version: 0.2.0
- name: query-service
  repository: "file://./signoz-charts/query-service"
  version: 0.2.2
- name: frontend
  repository: "file://./signoz-charts/frontend"
  version: 0.2.3
```

- Edit `values.yaml`

```
$ rm -rf values.yaml
$ touch values.yaml
$ vim values.yaml
```

- Edit `config/samples/monitor_v1_signoz.yaml`

```
$ rm -rf config/samples/monitor_v1_signoz.yaml
$ touch config/samples/monitor_v1_signoz.yaml
$ vim config/samples/monitor_v1_signoz.yaml
```

- Opertor build and Push to registry

```
$ export IMG=dther/signoz-operator:v0.07
$ make docker-build docker-push IMG=$IMG
```

- Install CRD on cluster and deploy operator on Cluster

```
$ make install 
$ make deploy IMG=$IMG
```

- Install Signoz on Cluster

```
$ kubectl create -f config/samples/monitor_v1_signoz.yaml
```
