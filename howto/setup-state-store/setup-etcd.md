# Setup etcd 

## Locally

You can run etcd locally using Docker:

```
docker run -d --name etcd bitnami/etcd
```

You can then interact with the server using `localhost:2379`.

## Kubernetes

The easiest way to install etcd on Kubernetes is by using the [Helm chart](https://github.com/helm/charts/tree/master/incubator/etcd):

```
helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
helm install --name etcd incubator/etcd
```

This will install etcd into the `default` namespace.
To interact with etcd, find the service with: `kubectl get svc etcd-etcd`.

For example, if installing using the example above, the etcd host address would be:

`etcd-etcd.default.svc.cluster.local:2379`

## Create a Dapr component

The next step is to create a Dapr component for etcd.

Create the following YAML file named `etcd.yaml`:

```
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: <name>
spec:
  type: state.etcd
  metadata:
  - name: endpoints
    value: <REPLACE-WITH-COMMA-DELIMITED-ENDPOINTS> # Required. Example: "etcd-etcd.default.svc.cluster.local:2379"
  - name: dialTimeout
    value: <REPLACE-WITH-DIAL-TIMEOUT> # Required. Example: "5s"
  - name: operationTimeout
    value: <REPLACE-WITH-OPERATION-TIMEOUT> # Optional. default: "10S"
```

The above example uses secrets as plain strings. It is recommended to use a secret store for the secrets as described [here](../../concepts/components/secrets.md)


## Apply the configuration

### In Kubernetes

To apply the etcd state store to Kubernetes, use the `kubectl` CLI:

```
kubectl apply -f etcd.yaml
```

### Running locally

The Dapr CLI will automatically create a directory named `components` in your current working directory with a Redis component.
To use etcd, replace the redis.yaml file with the etcd.yaml above.
