# CI/CD Process (Whole process is free and automatically!)
>*github (code repo) -> dockerhub (image repo) -> AWS EC2 (update the container)*

>Its a personal preference, sometimes we need to fill in unnecessary things with the GUI, so i'd rather using CLI, but AWS is actually even have the cron scheduler that you can click click, it's pretty thorough 

**When updates of code being pushed with tag such as x.x.x it will trigger the whole process!**
```
git tag -a 0.0.1 -m "Test release workflow"
git push --tags
```

## FIRST
### Github -> Dockerhub
1. You can find the workflow "Release to Docker Hub"
2. Create Personal Access Token (PAT) at Docker Hub
3. Add docker id & PAT to secrets of the repo (DOCKER_HUB_USERNAME & DOCKER_HUB_ACCESS_TOKEN) (Settings > Secrets > New secret)

## SECOND
### Dockerhub -> Docker container in AWS EC2 (pull latest images and run a container with it automatically)
1. Launch EC2 instance (free tier one)
2. Add inbound rules in the security group of your EC2 instance-> http 0.0.0.0/0
3. Install docker
4. Create crontab -> * * * * * sh ~/.docker/cron.sh
5. Create file ./docker/cron.sh :
```#!/bin/sh
if grep -Fqe "Image is up to date" << EOF
`docker pull dockerid/image`
EOF
then
    #echo "no update, just do cleaning"
    docker system prune --force
else
    #echo "newest exist, run new container!"
    docker stop container-name
    docker run -d --name container-name -p host-port:container-port dockerid/image
fi
```


