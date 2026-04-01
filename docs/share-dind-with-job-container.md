# Share dind with job container

You can weaken isolation and allow jobs to call docker commands.

## Limitations

-

## Example Values

```yaml
    config: |
      log:
        level: debug
      cache:
        enabled: false
      container:
        require_docker: true
        docker_timeout: 300s

## Specify an existing token secret
##
existingSecret: "runner-token2"
existingSecretKey: "token"

## Specify the root URL of the Gitea instance
giteaRootURL: "http://192.168.1.2:3000"
```

Now you can run docker commands inside your jobs.
