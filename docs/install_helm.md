# Install with Helm

## Getting started

The only officially supported way to install DB Operator is using a helm chart.

You can find the source code of our helm charts here: <https://github.com/db-operator/charts>

The charts are released as s simple helm repo as well as a an OCI artifact.

To install the repo, run the following

```sh
$ helm repo add db-operator https://db-operator.github.io/charts
$ helm search repo db-operator
```

OCI artifacts are available under `ghcr.io/db-operator/charts/`

To install the chart, run the following:

```sh
$ helm install db-operator/db-operator
# -- Or OCI
$ helm install ghcr.io/db-operator/charts/db-operator:${CHART_VERSION}
```

More info about the db-operator chart can be found in the [README.md](https://github.com/db-operator/charts/tree/main/charts/db-operator)

## Configure the operator via values

> @allaner chart will be updated soon, and then I'll write a better docs
