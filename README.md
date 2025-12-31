# Install camel-k operator

## Install prerequisites
TODO

## Install k3d cluster
TODO

## Install operator with helm

Install camel-k Operator thru Helm in camel-k namespace.

```sh
kubectl create ns camel-k
helm repo add camel-k https://apache.github.io/camel-k/charts/
helm install camel-k camel-k/camel-k -n camel-k
```

## Create repository secret

Create a docker repository secret named “registry-secret” in the namespace `camel-k`. If using Github use `ghcr.io/<my-user>` as repo. First create a Github token. Then use that token as password for the secret:

```sh
kubectl create secret docker-registry registry-secret --docker-server ghcr.io --docker-username <my-user> --docker-password <my-password>
```

## Install IntegrationPlatform

Apply this manifest to install the IntegrationPlatform with your registry address

```sh
apiVersion: camel.apache.org/v1
kind: IntegrationPlatform
metadata:
  labels:
    app: camel-k
  name: camel-k
spec:
  build:
    buildConfiguration:
      platforms:
        - linux/amd64
        - linux/arm64
    registry:
      address: ghcr.io/<my-user>
      secret: registry-secret

```

## Create and test sample integration
Creat sample route with `camel init hello.yaml`. Then run it with `kamel run hello.yaml`. List running integration with `kamel get`. Show log of running integration with `kamel log hello`.

## Dry run
Run integration in dry-run mode to see generated resources without applying them to the cluster:

`kamel run  hello.yaml -t camel.runtime-provider=plain-quarkus -o yaml > integration.yaml`

`kubectl apply -f integration.yaml`

## Promote
While integration is running, open second terminal.
Get the name of the running integration with: `kamel get` Then run: `kamel promote integration-name -n camel-k --to prod  -o yaml > integration.yaml`

Edit and apply the generated integration.yaml from above command to another namespace or cluster. Create missing ConfigMaps, Secrets first!

## Uninstall integration
Uninstall running kamel integration with: `kamel delete hello`

## Howto

### camel or kamel
Do not mix **k**amel and **c**amel integrations, routes and commands!

- kamel command works with the camel-k operator
- camel command works for integrations without the operator

###
Run integration with plain quarkus provider

`kamel run hello.yaml -t camel.runtime-provider=plain-quarkus`