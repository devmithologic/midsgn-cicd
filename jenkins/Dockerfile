# WHAT: extend the official Jenkins LTS image with extra tooling
# WHY:  the base image does not include Docker CLI or kubectl,
#       which Jenkins needs to build images and deploy to Kubernetes
FROM jenkins/jenkins:lts

# WHAT: switch to root to install system packages
# WHY:  apt-get requires root privileges
USER root

# WHAT: install Docker CLI and kubectl
# WHY:  Jenkins needs docker to build images and kubectl to deploy to Kubernetes
#       Both binaries must exist inside the container
RUN apt-get update && \
    apt-get install -y docker.io curl && \
    curl -LO "https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm kubectl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# WHAT: pre-create the kubeconfig file as an empty file
# WHY:  Docker bind-mounts a host file correctly only if the target already
#       exists as a file in the image; otherwise Docker creates a directory
RUN mkdir -p /root/.kube && touch /root/.kube/jenkins-kubeconfig

# WHAT: switch back to the jenkins user
# WHY:  running Jenkins as root is a security risk in production;
#       we only needed root temporarily to install packages
USER jenkins
