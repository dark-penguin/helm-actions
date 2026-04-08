# Gitea Actions Helm Chart

This helm chart serves as the way to deploy the Gitea [act-runners](https://gitea.com/gitea/act_runner) alongside a running Gitea instance.  
It serves as a standalone chart and does not rely on Gitea to be present in the same environment, however it needs to be able to reach a Gitea instance to function.  
The parameters which can be used to customize the deployment are described below, check those out if you want to see if something is supported.  

If you want to propose a new feature or mechanism, submit an [issue here](https://gitea.com/gitea/helm-actions/issues).

## Quick-start

[Documentation](./docs/README.md)

To get started, add the Helm repo, assuming you have not already:

```sh
helm repo add gitea-charts https://dl.gitea.com/charts/
helm repo update
```

Then pull the values.yaml file and fill it accordingly.

```sh
helm show values gitea-charts/actions > values.yaml
```

Deploy with your values, make sure the path is correct:

```sh
helm upgrade --install gitea-actions gitea-charts/actions -f values.yaml
```

You should be good to go!

### Runner Token Secret Template

For reference, a template for the secret is given below:  

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: runner-secret
  namespace: "my-gitea-namespace"
type: Opaque
stringData:
    runner-token: "my-cool-runner-token-given-by-gitea"
```

### Rootless Options

If `.Values.statefulset.dind.rootless: true` is set, then the following will be required:  
`.Values.statefulset.dind.tag` must be a rootless image such as: `29.3.1-dind-rootless`

## Parameters

### Gitea Actions

| Name                                      | Description                                                                                                                                 | Value                          |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| `enabled`                                 | Create an act runner StatefulSet.                                                                                                           | `false`                        |
| `statefulset.replicas`                    | the amount of (replica) runner pods deployed                                                                                                | `1`                            |
| `statefulset.timezone`                    | is the timezone that will be set in the act_runner image                                                                                    | `Etc/UTC`                      |
| `statefulset.annotations`                 | Act runner annotations                                                                                                                      | `{}`                           |
| `statefulset.labels`                      | Act runner labels                                                                                                                           | `{}`                           |
| `statefulset.resources`                   | Act runner resources                                                                                                                        | `{}`                           |
| `statefulset.nodeSelector`                | NodeSelector for the statefulset                                                                                                            | `{}`                           |
| `statefulset.tolerations`                 | Tolerations for the statefulset                                                                                                             | `[]`                           |
| `statefulset.affinity`                    | Affinity for the statefulset                                                                                                                | `{}`                           |
| `statefulset.extraVolumes`                | Extra volumes for the statefulset                                                                                                           | `[]`                           |
| `statefulset.persistence.size`            | Size for persistence to store act runner data                                                                                               | `1Gi`                          |
| `statefulset.securityContext`             | Customize the SecurityContext                                                                                                               | `{}`                           |
| `statefulset.serviceAccountName`          | Customize the service account name                                                                                                          | `""`                           |
| `statefulset.hostAliases`                 | Inject entries into the /etc/hosts file                                                                                                     | `[]`                           |
| `statefulset.actRunner.registry`          | image registry, e.g. gcr.io,docker.io                                                                                                       | `docker.gitea.com`             |
| `statefulset.actRunner.repository`        | The Gitea act runner image                                                                                                                  | `act_runner`                   |
| `statefulset.actRunner.tag`               | The Gitea act runner tag                                                                                                                    | `0.3.0`                        |
| `statefulset.actRunner.digest`            | Image digest. Allows to pin the given image tag. Useful for having control over mutable tags like `latest`                                  | `""`                           |
| `statefulset.actRunner.pullPolicy`        | The Gitea act runner pullPolicy                                                                                                             | `IfNotPresent`                 |
| `statefulset.actRunner.fullOverride`      | Completely overrides the image registry, path/image, tag and digest.                                                                        | `""`                           |
| `statefulset.actRunner.extraVolumeMounts` | Allows mounting extra volumes in the act runner container                                                                                   | `[]`                           |
| `statefulset.actRunner.config`            | Act runner custom configuration. See [Act Runner documentation](https://docs.gitea.com/usage/actions/act-runner#configuration) for details. | `Too complex. See values.yaml` |
| `statefulset.actRunner.extraEnvs`         | Allows adding custom environment variables                                                                                                  | `[]`                           |
| `statefulset.dind.rootless`               | a simple flag to let helm know we are dealing with a rootless dind container                                                                | `false`                        |
| `statefulset.dind.uid`                    | a field to set the running user id for the rootless dind container, so it knows where to look for the socket                                | `""`                           |
| `statefulset.dind.registry`               | image registry, e.g. gcr.io,docker.io                                                                                                       | `docker.io`                    |
| `statefulset.dind.repository`             | The Docker-in-Docker image                                                                                                                  | `docker`                       |
| `statefulset.dind.tag`                    | The Docker-in-Docker image tag                                                                                                              | `29.3.1-dind`                  |
| `statefulset.dind.digest`                 | Image digest. Allows to pin the given image tag. Useful for having control over mutable tags like `latest`                                  | `""`                           |
| `statefulset.dind.fullOverride`           | Completely overrides the image registry, path/image, tag and digest.                                                                        | `""`                           |
| `statefulset.dind.pullPolicy`             | The Docker-in-Docker pullPolicy                                                                                                             | `IfNotPresent`                 |
| `statefulset.dind.extraVolumeMounts`      | Allows mounting extra volumes in the Docker-in-Docker container                                                                             | `[]`                           |
| `statefulset.dind.extraEnvs`              | Allows adding custom environment variables, such as `DOCKER_IPTABLES_LEGACY`                                                                | `[]`                           |

### Gitea Actions Init

| Name                      | Description                                                                                                | Value          |
| ------------------------- | ---------------------------------------------------------------------------------------------------------- | -------------- |
| `init.image.registry`     | image registry, e.g. gcr.io,docker.io                                                                      | `""`           |
| `init.image.repository`   | The init image                                                                                             | `busybox`      |
| `init.image.tag`          | the init image tag                                                                                         | `1.37.0`       |
| `init.image.digest`       | Image digest. Allows to pin the given image tag. Useful for having control over mutable tags like `latest` | `""`           |
| `init.image.pullPolicy`   | The init image pullPolicy                                                                                  | `IfNotPresent` |
| `init.image.fullOverride` | Completely overrides the image registry, path/image, tag and digest.                                       | `""`           |

### Runner Token Secret Configuration

| Name                | Description                    | Value |
| ------------------- | ------------------------------ | ----- |
| `existingSecret`    | Secret that contains the token | `""`  |
| `existingSecretKey` | Secret key                     | `""`  |

### Gitea URL Setting

| Name           | Description                                   | Value |
| -------------- | --------------------------------------------- | ----- |
| `giteaRootURL` | URL the act_runner registers and connect with | `""`  |

### Extra Init Containers

| Name                      | Description                                                                                     | Value |
| ------------------------- | ----------------------------------------------------------------------------------------------- | ----- |
| `preExtraInitContainers`  | Additional init containers to run in the pod before Gitea-actions runs it owns init containers. | `[]`  |
| `postExtraInitContainers` | Additional init containers to run in the pod after Gitea-actions runs it owns init containers.  | `[]`  |

### Global

| Name                      | Description                        | Value |
| ------------------------- | ---------------------------------- | ----- |
| `global.imageRegistry`    | global image registry override     | `""`  |
| `global.imagePullSecrets` | global image registry pull secrets | `[]`  |
| `global.storageClass`     | global storage class override      | `""`  |
