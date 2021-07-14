# Docker Gitlab with Docker runner

# Quickstart

Add a host entry:

`echo '127.0.0.1 gitlab.local' | sudo tee -a /etc/hosts `

Bring it up, sudo because we're using privileged ports:

`sudo docker-compose up -d`

After several minutes get your root password:

`docker-compose exec gitlab /usr/bin/cat /etc/gitlab/initial_root_password`

Go to http://gitlab.local:8880/ and log in as root with the above password.

Click on menu -> admin -> runners and copy the registration token, then replace
the one in the command below and run it:

```
docker compose exec gitlab-runner gitlab-runner register \
    --non-interactive \
    --executor="docker" \
    --custom_build_dir-enabled="true" \
    --docker-image="docker" \
    --url="http://gitlab:80" \
    --clone-url="http://gitlab:80" \
    --registration-token="@@@@@REPLACE_WITH_YOUR_REGISTRATION_TOKEN@@@@@" \
    --description="docker-runner" \
    --tag-list="docker" \
    --run-untagged="true" \
    --locked="false" \
    --docker-network-mode="gitlab-docker_gitlab" \
    --docker-disable-cache="true" \
    --docker-privileged="true" \
    --cache-dir="/cache" \
    --builds-dir="/builds" \
    --docker-volumes="gitlab-runner-builds:/builds" \
    --docker-volumes="gitlab-runner-cache:/cache"
```

# What This Does

Brings up a Gitlab CE instance with a runner which itself runs docker but which
connects to the docker host that it is running on to bring up other docker
containers, all in order to facilitate isolation and the use of docker images
for Gitlab pipelines.


# Sources

- https://www.ivankrizsan.se/2019/06/17/building-in-docker-containers-on-gitlab-ce
