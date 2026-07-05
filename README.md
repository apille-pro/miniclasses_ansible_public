# Mini-Classes Ansible : Labs 100% Locaux & Dockerisés

Bienvenue sur le dépôt des Labs associés aux "Mini-Classes Ansible". 

Ce projet a été conçu pour offrir un environnement de pratique **rapide, gratuit, auditable et 100% local**, idéal pour des sessions de formation courtes ou du prototypage.

## 🎯 L'Objectif
Il existe de nombreuses ressources d'excellente qualité pour apprendre Ansible.
L'approche de ce dépôt est complémentaire : **partir de zéro pour créer un bac à sable local en quelques secondes via Docker**, sans dépendre d'instances Cloud ni de machines virtuelles lourdes.

## 🛠️ Architecture Technique (Le Setup)
Ce projet a avant tout été conçu pour un environnement de travail Windows / WSL2, mais il devrait de fait également fonctionner sans souci sous Linux & Mac.

L'infrastructure cible repose sur :
* **Docker Compose :** Instanciation de nœuds "Cibles" (`target-web` et `target-db` dans les labs 1 à 5).
* **Image Base :** `ubuntu:22.04`
* **Systemctl Shim :** L'image Docker intègre un *shim* Python remplaçant l'init `systemd`. Cela permet d'utiliser le module Ansible natif `service`/`systemd` à l'intérieur du conteneur sans avoir à contourner la sécurité de Docker (pas de mode `--privileged` extrême, pas de conflits de cgroups). Le *shim* utilisé est le projet [docker-systemctl-replacement de Guido U. Draheim](https://github.com/gdraheim/docker-systemctl-replacement).

## 📚 Contenu des Labs
Chaque lab permet de pratiquer sur des notions précises :
* **[Lab 1](./lab1/) :** Connectivité, architecture et commandes Ad-Hoc.
* **[Lab 2](./lab2/) :** Variables, Inventaires et Configuration Ansible.
* **[Lab 3](./lab3/) :** Playbooks, Idempotence, Modules natifs et Templates Jinja2.
* **[Lab 4](./lab4/) :** Industrialisation, structuration en Rôles.
* **[Lab 5](./lab5/) :** Écosystème Galaxy.

Ces labs bâtissent les uns sur les autres : ils partent du setup de l'environnement (lab1) pour arriver au déploiement d'un environnement contenant une Base De Données MariaDB et un serveur Nginx (lab5).

Chaque lab contient un énoncé `README_lab<X>.md`, ainsi que la solution `solution_lab<X>.md`.

## 🚀 Démarrage Rapide
1. Clonez le dépôt.
2. Suivez le [Readme du lab1](./lab1/README_lab1.md)
3. L'environnement est prêt, à vous de jouer !

A noter que vous pouvez facilement customiser l'environnement sur lequel vous souhaitez déployer (ajout/retrait de nœuds cibles notamment) en modifiant le `docker-compose.yml`.

## Auteur & Citations
Ce projet a été conçu et rédigé par **Antoine PILLE**.
Si vous utilisez ce support pour vos propres formations, merci de citer la source originale : [miniclasses_ansible_public](https://github.com/apille-pro/miniclasses_ansible_public).
