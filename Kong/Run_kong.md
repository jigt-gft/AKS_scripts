## Kong with html official manifest
To setup Kong Ingress Controller excute:
#### YAML:
```
kubectl apply -f https://bit.ly/kong-ingress
```
#### HELM:
```
helm install stable/kong --set ingressController.enabled=true
```

You can also spin up Kong Ingress Controller without a database dependency:
#### YAML
```
kubectl apply -f https://bit.ly/kong-ingress-dbless
```
#### HELM
```
helm install stable/kong --set ingressController.enabled=true \
  --set postgresql.enabled=false --set env.database=off
```

## Kong from official repository

Download repository from https://github.com/Kong/kong-dist-kubernetes/

create the Kong namespace

```
$ kubectl apply -f kong-namespace.yaml
```

#### Cassandra Backed Kong
Use the cassandra-service.yaml and cassandra-statefulset.yaml file from this repository to deploy a Cassandra Service and a StatefulSet in the cluster.
```
$ kubectl apply -f cassandra-service.yaml
$ kubectl apply -f cassandra-statefulset.yaml
```

Use the kong-control-plane-cassandra.yaml file from this repository to run required migrations and deploy Kong control plane node including the Kong admin api

```
$ kubectl -n kong apply -f kong-control-plane-cassandra.yaml
```

Use the kong-ingress-data-plane-cassandra.yaml file from this repository to run the Kong data plane node
```
$ kubectl -n kong apply -f kong-ingress-data-plane-cassandra.yaml
```

#### PostgreSQL Backed Kong

Use the postgres.yaml file from the repository to deploy a PostgreSQL Service and a ReplicationController in the cluster:
```
$ kubectl create -f postgres.yaml
```
Use the kong-control-plane-postgres.yaml file from this repository to run required migrations and deploy Kong control plane node including the Kong Admin API:
```
$ kubectl -n kong apply -f kong-control-plane-postgres.yaml
```
Use the kong-ingress-data-plane-postgres.yaml file from this repository to run the Kong data plane node
```
$ kubectl -n kong apply -f kong-ingress-data-plane-postgres.yaml
```

#### Using Datastore Backed Kong
First let’s ensure the Kong control plane and data plane are successfully running
```
kubectl get all -n kong

NAME                           READY   STATUS
pod/kong-control-plane         1/1     Running
pod/kong-ingress-data-plane    1/1     Running
```

Get access to the Kong Admin API port (if running minikube the below should work):
```
$ export HOST=$(kubectl get nodes --namespace default -o jsonpath='{.items[0].status.addresses[0].address}')
$ export ADMIN_PORT=$(kubectl get svc --namespace kong kong-control-plane  -o jsonpath='{.spec.ports[0].nodePort}')
```