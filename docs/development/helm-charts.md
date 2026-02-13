# Helm Chart development

The repository where the helm charts are being developed can be found here: <https://github.com/db-operator/charts>

## Tools

Tools that are used for development:

- helm: <https://helm.sh>
- helmfile (used for testing): <https://helmfile.readthedocs.io/en/latest/>
- helm-unittest: <https://github.com/helm-unittest/helm-unittest>
- chart-testing: <https://github.com/helm/chart-testing>
- helm-docs: <https://github.com/norwoodj/helm-docs>
- pre-commit: <https://pre-commit.com>

## CRD management

In our case it seemed like a good idea to install CRDs using the helm templates, as the db-operator chart itself doesn't depend on them. In order not to maintain them by hands, we've written a script `./scripts/sync_crds.sh`, that is getting a desired version from the db-operator's `Chart.yaml` and syncs CRDs from the operator repository.

Let's say you have changed something in CRDs and want to test your change with the helm chart before the change is merged in the operator repository.

```shell
yq -i '.appVersion = "${GIT_SHA}"' ./charts/db-operator/Chart.yaml
./scripts/sync_crds.sh
```

After that, CRDs will be up-to-date with the operator repo and you can test your changes.
