FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# 1. Installation des outils de base
RUN apt-get update && apt-get install -y \
    openssh-server \
    sudo \
    python3 \
    python3-apt \
    curl \
    && rm -rf /var/lib/apt/lists/*

# 2. Installation du Systemctl Shim (Version Python 3 - v1.7.1097)
# Note : On utilise l'URL 'raw' pour récupérer directement le script sans le HTML de GitHub
RUN curl -L https://raw.githubusercontent.com/gdraheim/docker-systemctl-replacement/v1.7.1097/files/docker/systemctl3.py -o /usr/local/bin/systemctl && \
    chmod +x /usr/local/bin/systemctl

# 3. Setup de l'utilisateur 'deploy'
RUN useradd -m -s /bin/bash deploy && \
    echo "deploy:ansible" | chpasswd && \
    echo "deploy ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# 4. Préparation SSH
RUN mkdir -p /home/deploy/.ssh && \
    chown deploy:deploy /home/deploy/.ssh && \
    chmod 700 /home/deploy/.ssh

RUN mkdir /var/run/sshd

# Processus principal
CMD ["/usr/sbin/sshd", "-D"]