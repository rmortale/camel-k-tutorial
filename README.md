# Install camel-k operator


## Create repository secret

Create a docker repository secret named “registry-secret” in the namespace `camel-k`. If using Github use `ghcr.io/<my-user>` as repo. First create a Github token. Then use that token as password for the secret:

```sh
kubectl create secret docker-registry registry-secret --docker-server ghcr.io --docker-username <my-user> --docker-password <my-password>
```

## Install operator with helm

Install camel-k Operator thru Helm in camel-k namespace.


```sh
helm repo add camel-k https://apache.github.io/camel-k/charts/
helm install camel-k camel-k/camel-k -n camel-k
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

## Test
Test and run an integration with `kamel –dev run integration.yaml`

## Promote
While integration is running, open second terminal.
Get the name of the running integration with: `kamel get` Then run: `kamel promote integration-name -n camel-k --to prod  -o yaml > integration.yaml`

Apply the generated integration.yaml from above command. Create missing ConfigMaps, Secrets first!

## Howto

### camel or kamel
Do not mix **k**amel and **c**amel integrations, routes and commands!

- kamel command works with the camel-k operator
- camel command works for integrations without the operator

###
Run integration with plain quarkus provider

`kamel run kafka.java -t camel.runtime-provider=plain-quarkus`