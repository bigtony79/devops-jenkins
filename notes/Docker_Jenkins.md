# Docker with Jenkins

Since Jenkins is running as a Docker container on the host droplet, we need to mount the host machine’s Docker runtime directory into the Jenkins container. This allows Jenkins to execute Docker commands (such as building and pushing images) directly on the host.

