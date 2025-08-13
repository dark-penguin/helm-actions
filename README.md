# helm-act

## Rootless Defaults

If `.Values.image.rootless: true`, then the following will occur. In case you use `.Values.image.fullOverride`, check that this works in your image:

- If `.Values.provisioning.enabled: true`, then uses the rootless Gitea image, must match helm-Gitea.

## Parameters

### Gitea Actions

| Name                                      | Description                                                                                                                                 | Value                          |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| `enabled`                                 | Create an act runner StatefulSet.                                                                                                           | `false`                        |
| `init.image.repository`                   | The image used for the init containers                                                                                                      | `busybox`                      |
| `init.image.tag`                          | The image tag used for the init containers                                                                                                  | `1.37.0`                       |
| `statefulset.replicas`                    | the amount of (replica) runner pods deployed                                                                                                | `1`                            |
| `statefulset.annotations`                 | Act runner annotations                                                                                                                      | `{}`                           |
| `statefulset.labels`                      | Act runner labels                                                                                                                           | `{}`                           |
| `statefulset.resources`                   | Act runner resources                                                                                                                        | `{}`                           |
| `statefulset.nodeSelector`                | NodeSelector for the statefulset                                                                                                            | `{}`                           |
| `statefulset.tolerations`                 | Tolerations for the statefulset                                                                                                             | `[]`                           |
| `statefulset.affinity`                    | Affinity for the statefulset                                                                                                                | `{}`                           |
| `statefulset.extraVolumes`                | Extra volumes for the statefulset                                                                                                           | `[]`                           |
| `statefulset.actRunner.repository`        | The Gitea act runner image                                                                                                                  | `gitea/act_runner`             |
| `statefulset.actRunner.tag`               | The Gitea act runner tag                                                                                                                    | `0.2.11`                       |
| `statefulset.actRunner.pullPolicy`        | The Gitea act runner pullPolicy                                                                                                             | `IfNotPresent`                 |
| `statefulset.actRunner.extraVolumeMounts` | Allows mounting extra volumes in the act runner container                                                                                   | `[]`                           |
| `statefulset.actRunner.config`            | Act runner custom configuration. See [Act Runner documentation](https://docs.gitea.com/usage/actions/act-runner#configuration) for details. | `Too complex. See values.yaml` |
| `statefulset.dind.repository`             | The Docker-in-Docker image                                                                                                                  | `docker`                       |
| `statefulset.dind.tag`                    | The Docker-in-Docker image tag                                                                                                              | `25.0.2-dind`                  |
| `statefulset.dind.pullPolicy`             | The Docker-in-Docker pullPolicy                                                                                                             | `IfNotPresent`                 |
| `statefulset.dind.extraVolumeMounts`      | Allows mounting extra volumes in the Docker-in-Docker container                                                                             | `[]`                           |
| `statefulset.dind.extraEnvs`              | Allows adding custom environment variables, such as `DOCKER_IPTABLES_LEGACY`                                                                | `[]`                           |
| `statefulset.persistence.size`            | Size for persistence to store act runner data                                                                                               | `1Gi`                          |
| `existingSecret`                          | Secret that contains the token                                                                                                              | `""`                           |
| `existingSecretKey`                       | Secret key                                                                                                                                  | `""`                           |
| `giteaRootURL`                            | URL the act_runner registers and connect with                                                                                               | `""`                           |

### Global

| Name                   | Description                    | Value |
| ---------------------- | ------------------------------ | ----- |
| `global.imageRegistry` | global image registry override | `""`  |
| `global.storageClass`  | global storage class override  | `""`  |
