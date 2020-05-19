# Watchtower

This service runs on CCAO's Shiny server/Ubuntu VM and is responsible for keeping CCAO Shiny applications up-to-date. It utilizes a small application called [Watchtower](https://containrrr.github.io/watchtower/) to scan the [CCAO container registry](https://gitlab.com/groups/ccao-data-science---modeling/-/container_registries) at a set interval and automatically pull new Docker images. 

The service will only scan/update images with the Docker label `com.centurylinklabs.watchtower.enable=true`. This label must be [added manually](https://gitlab.com/ccao-data-science---modeling/applications/shiny_app_pinval/-/blob/staging/Dockerfile#L130) when creating CCAO Shiny applications. Once an image is updated, any new sessions launched by ShinyProxy will use the updated version. To force a new session/app update, logout of ShinyProxy, log back in, then relaunch the application.

## Usage

To start this service, first connect to the Shiny server [via SSH](https://support.rackspace.com/how-to/connecting-to-a-server-using-ssh-on-linux-or-mac-os/) (ask IT for login details). Next, go to the folder containing this repository (usually `~/services/service_watchtower`) or clone the repo if it doesn't exist locally. Finally, start the service using [Docker Compose](https://docs.docker.com/compose/gettingstarted/) by typing `docker-compose up -d` while in the same folder as `docker-compose.yml`. 

This service requires Docker login details in order to scan CCAO's private container registry. These details are passed to the service by placing a file, `config.json`, within a subdirectory of the main repository folder called `secrets`. The directory structure should be:

```
service_watchtower
├── docker-compose.yaml
└── secrets
    └── config.json
```

The `config.json` file is generated using [`docker login`](https://docs.docker.com/engine/reference/commandline/login/) and saved to `~/.docker/config.json` by default. Be sure to copy the file into `secrets` before launching the service with `docker-compose up -d`. Login details for `docker login` can be generated using GitLab's [Group Deploy Tokens](https://docs.gitlab.com/ee/user/project/deploy_tokens/#group-deploy-token), created within the [`ccao-data-science---modeling`](https://gitlab.com/ccao-data-science---modeling) group.