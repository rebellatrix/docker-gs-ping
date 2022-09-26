CI/CD Process (Whole process is free and automatically!)
github (code repo) -> dockerhub (image repo) -> AWS EC2 (update the container)

When updates of code being pushed with tag such as *.*.* it will trigger the whole process!
git tag -a 0.0.1 -m "Test release workflow"
git push --tags

1. Github -> Dockerhub
You can find the workflow "Release to Docker Hub"
Create Personal Access Token (PAT) at Docker Hub
Add docker id & PAT to secrets of the repo (DOCKER_HUB_USERNAME & DOCKER_HUB_ACCESS_TOKEN) (Settings > Secrets > New secret)

2. Dockerhub -> Docker container in AWS EC2 (pull latest images and run a container with it automatically)
Launch EC2 instance (free tier one)
Add inbound rules in the security group of your EC2 instance-> http 0.0.0.0/0
Install docker
Create crontab -> * * * * * sh ~/.docker/cron.sh
Create file ./docker/cron.sh :
#!/bin/sh
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


