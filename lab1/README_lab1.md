# README_lab1

Ce README contient toutes les instructions nécessaires pour installer l'environnement de lab. Les labs pourront être réalisés entre chaque session / mini-classe.

## Sommaire

1. [Installation de WSL2](#installation-de-wsl2)
2. [Configuration de Docker Engine & Compose](#configuration-de-docker-engine--docker-compose-sous-wsl2)
    * [1. Activation de Systemd](#1-activation-de-systemd-dans-wsl2)
    * [2. Installation de Docker Engine](#2-installation-de-docker-engine)
    * [3. Configuration Post-Installation](#3-configuration-post-installation)
    * [4. Vérification de l'installation](#4-vérification-de-linstallation)
3. [Mise en place de l'infrastructure du Lab](#mise-en-place-de-linfrastructure-du-lab)
    * [A. Installation de Ansible](#a-installation-de-ansible-en-local-wsl2-une-seule-fois)
    * [B. Génération de la clé SSH](#b-génération-de-la-clé-spécifique-une-seule-fois)
    * [C. Lancement de l'infrastructure](#c-lancement-de-linfrastructure)
4. [A vous de jouer !](#a-vous-de-jouer-)
    * [Validation de l'environnement](#validation-complète-de-lenvironnement-des-labs)
    * [Commandes Ansible ad-hoc](#commandes-ansible-ad-hoc)

## Installation de WSL2

A vous de jouer, la procédure est bien documentée sur internet !

Choisissez la distribution Ubuntu.

## Configuration de Docker Engine & Docker Compose sous WSL2

Ce guide décrit l'installation de **Docker Engine** (natif) dans une distribution WSL2 (Ubuntu/Debian), en utilisant `systemd` pour la gestion du daemon, sans dépendre de Docker Desktop.

### 1. Activation de Systemd dans WSL2

`systemd` est nécessaire pour que le service Docker démarre automatiquement avec votre distribution.

1. Dans votre terminal WSL2, éditez le fichier de configuration :
```bash
sudo vim /etc/wsl.conf
```


2. Ajoutez (ou modifiez) les lignes suivantes :
```ini
[boot]
systemd=true
```

Nota : il est tout à fait possible que la variable soit déjà présente et à `true`. Dans ce cas, passez directement à la section suivante.

3. Fermez votre terminal et, depuis un **PowerShell Windows**, redémarrez WSL pour appliquer les changements :
```powershell
wsl --shutdown
```


### 2. Installation de Docker Engine

Réouvrez votre terminal WSL2 et exécutez les commandes suivantes pour installer la version officielle.

#### Dépendances et Clé GPG

```bash
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release -y

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

```

#### Ajout du dépôt Docker

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```

#### Installation des paquets

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-v2 -y
## Nota : si vous prenez une erreur sur cette dernière commande, essayer plutôt:
## sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 3. Configuration Post-Installation

Pour utiliser Docker sans `sudo`, ajoutez votre utilisateur au groupe `docker` :

```bash
sudo usermod -aG docker $USER

```

*Note : Redémarrez votre session WSL2 (fermer/ouvrir le terminal) pour que ce changement soit effectif.*

### 4. Vérification de l'installation

Une fois la session redémarrée, vérifiez que tout fonctionne :

| Test | Commande |
| --- | --- |
| **Daemon** | `systemctl status docker` |
| **Docker** | `docker run hello-world` |
| **Compose** | `docker compose version` |


### 💡 Notes pour le développement sous WSL2

* **Emplacement du code :** Pour des performances optimales, placez vos projets dans le système de fichiers Linux (ex: `/home/$USER/projets/`) et non dans le montage Windows (`/mnt/c/Users/...`).
* **Accès réseau :** Vos conteneurs sont accessibles via `localhost` depuis votre navigateur Windows grâce au "Localhost forwarding" de WSL2.

## Mise en place de l'infrastructure du Lab

Pour le Ansible controller, nous utiliserons tout simplement notre WSL2 local.
Pour simuler des VMs cibles, nous mettrons UP 2 containers.

Nota: l'image utilisée pour ce faire sera une image custom, répondant à nos besoins. En particulier, cette image permettra l'utilisation de systemd dans les containers (ce qui permettra de faire des `systemctl status <mon_service>` dedans).
Ce comportement n'est pas natif à Docker (il va contre le principe d'isolation), et fonctionne mal sous WSL/Ubuntu : même en jouant avec les privilèges des containers, même en donnant des accès aux cgroup. 
Pour contourner le problème nous utiliserons simplement un shim de systemd (cf. le `Dockerfile`). L'idée est simple : ne pas réellement instancier systemd (qui fait échouer le lancement des containers), mais un script qui remplira les fonctions de systemd. La pratique est discutable, néanmoins c'est complètement "fine" dans le cadre de ce lab.

### A. Installation de Ansible en local WSL2 (une seule fois)

Méthode d'installation préconisée par Stéphane ROBERT (https://blog.stephane-robert.info/docs/infra-as-code/gestion-de-configuration/ansible/decouvrir/installation-ansible/#m%C3%A9thode-1--pipx-recommand%C3%A9e) :

```bash
# 1. Installer pipx via apt (Ubuntu 24.04 + Debian 13 ont pipx natif)
sudo apt update
sudo apt install -y pipx
pipx ensurepath
exec $SHELL

# 2. Installer ansible
pipx install --include-deps ansible

# 3. Vérifier
ansible --version
```

### B. Génération de la clé spécifique (une seule fois)

`ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_miniclasses_ansible -N ""`

`-f` : définit le nom du fichier.
`-N ""` : définit une passphrase vide (pour simplifier le Lab, à déconseiller en prod).

### C. Lancement de l'infrastructure

Si ça n'est pas déjà fait, clonez le présent repo et placez vous dans ce répertoire "lab1" contenant le `Dockerfile` et le `docker-compose.yml`.

```bash
# Si l'image n'est pas déjà buildée
docker build -t ansible-lab-node:1.0.6 .

# Lancement des conteneurs depuis le docker-compose.yml
docker compose up -d
```

Si vous ouvrez le `docker-compose.yml` vous noterez que pour accéder aux conteneurs nous avons ici fait le choix de les mapper sur des ports différents (2221 pour web, 2222 pour bdd). Raison de ce choix : c'est simplement plus fiable que de chercher à résoudre des noms de domaine Docker internes depuis l'hôte Windows/WSL.


#### Remarques sur les fichiers Ansible présents dans ce répertoire

##### ansible.cfg : la configuration du moteur Ansible

Nous étudierons la configuration Ansible dans une prochaine session. Vous pouvez néanmoins déjà ouvrir ce fichier.
Ce fichier est notre configuration Ansible. Elle permet notamment d'indiquer le user distant à utiliser ainsi que la clé ssh à utiliser.

##### hosts.ini : l'inventaire Ansible

Nous étudierons les inventaires Ansible dans une prochaine session. Vous pouvez néanmoins déjà ouvrir ce fichier.
Ce fichier est notre inventaire Ansible. Un inventaire permet de lister et d'organiser les hosts cibles à configurer via Ansible.

##### test_connection.yml : un playbook Ansible

Nous étudierons les playbook Ansible dans une prochaine session. Vous pouvez néanmoins déjà ouvrir ce fichier.
Un playbook "orchestre" les tâches à jouer sur un ou des serveurs distants. Ici, ce playbook sert simplement à valider que nous parvenons bien à joindre nos "hôtes" distants (= nos containers), et que l'escalade des droits fonctionne bien.

## A vous de jouer !

### Validation complète de l'environnement des labs

Le but est maintenant que vous validiez votre installation. Dans WSL lancez :

```bash
ansible-playbook -i hosts.ini test_connection.yml
```

*"Connecté en tant que deploy, Sudo fonctionne en tant que root"* ? Bravo, votre installation fonctionne !

### Commandes Ansible ad-hoc

Reprenez les slides sur les commandes `ad-hoc` de la session 1. 

**=> Votre but est de pinger vos 2 "hosts" distant en utilisant le module Ansible dédié.**

INFO UTILE : pour des raisons pratiques (nous sommes sur localhost...) vous serez **obligés** d'utiliser l'inventaire afin d'obtenir le résultat attendu.
*Autre info/indice : pas besoin de préciser l'utilisateur ou la clé ssh grâce au fichier ansible.cfg !*

#### Résultat attendu

L'output de la command ad-hoc doit être :

```bash
target-db | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
target-web | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
