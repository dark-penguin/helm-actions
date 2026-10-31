# Native Kubernetes jobs

Gitea Runner 4.1.0 or later can create a Kubernetes pod for each job. This requires Kubernetes 1.30 or later and does not require a Docker daemon or privileged runner container.

Create a registration token Secret in the runner namespace, as described in the [quick-start](../README.md#runner-token-secret-template), then use these values:

```yaml
enabled: true
giteaRootURL: https://gitea.example.com
existingSecret: runner-secret
existingSecretKey: runner-token

serviceAccount:
  create: true
rbac:
  create: true

statefulset:
  replicas: 1
  dind:
    enabled: false
  runner:
    config: |
      log:
        level: info
      runner:
        capacity: 6
        labels:
          - ubuntu-latest:kubernetes://docker.gitea.com/runner-images:ubuntu-latest
      cache:
        enabled: false
      kubernetes:
        pod_template:
          spec:
            containers:
              - name: job
                resources:
                  requests:
                    cpu: 100m
                    memory: 128Mi
                  limits:
                    memory: 2Gi
```

Save this as `kubernetes-values.yaml` and install:

```sh
helm upgrade --install gitea-actions gitea-charts/actions \
  --namespace gitea-actions --create-namespace \
  -f kubernetes-values.yaml
```

Workflows select the backend with `runs-on: ubuntu-latest`. One runner manager can start up to six concurrent job pods with these values; the cluster still needs enough resources to schedule them.
`statefulset.replicas` controls the number of persistent managers, while `runner.capacity` limits concurrent jobs per manager. This does not autoscale managers or cluster nodes.

Replace the runner configuration as shown, including its labels, when disabling DinD. The chart's default configuration requires Docker.
The manager keeps its registration in the existing data volume. The runner creates job pods on demand and removes them and their temporary Secrets after execution.
Job pods do not mount a service account token by default.

## Service accounts and permissions

`serviceAccount.create` creates the manager's service account. Set `statefulset.serviceAccountName` to choose its name; otherwise the chart generates one.
To use an existing account, set `serviceAccount.create: false` and supply its name through `statefulset.serviceAccountName`.

`rbac.create` independently creates a Role and RoleBinding in the runner namespace, with these permissions:

| Resource | Verbs |
| --- | --- |
| `pods` | `create`, `get`, `list`, `delete` |
| `pods/exec` | `create`, `get` |
| `pods/log` | `get` |
| `secrets` | `create`, `delete` |

Use a dedicated namespace for jobs whose workflows you trust with these permissions. The Role covers all pods and Secrets in that namespace.
If permissions are managed externally, leave `rbac.create: false` and bind the required permissions to the runner's service account yourself.
If `kubernetes.namespace` targets a different namespace, configure the Role and binding there yourself; chart-managed RBAC only covers the runner namespace.

## Job configuration and compatibility

Configure job resources, scheduling and private image pull Secrets through `kubernetes.pod_template` in the runner configuration.
`statefulset.runner.resources`, scheduling options and `global.imagePullSecrets` apply to the manager, not the dynamically created job pods.
JavaScript actions need Node.js in the job image, and checkout needs Git. Services run alongside the job container in the same pod.

Docker actions, `docker://` steps and some Docker-specific container options are unsupported by this backend.
See the [runner's Kubernetes documentation](https://gitea.com/gitea/runner/src/branch/main/docs/kubernetes.md) for configuration and compatibility details.

For mixed workloads, keep `statefulset.dind.enabled: true`, enable the service account permissions, and configure distinct `docker://` and `kubernetes://` labels in the runner configuration.
Workflows select the appropriate label with `runs-on`; no chart-wide backend switch is needed.
