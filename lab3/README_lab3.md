# README_lab3

## A vous de jouer !

- Placez-vous dans ce sous-répertoire `lab3` et remettez UP vos containers (`docker compose up -d`).

**Attention** : pour les besoins de ce lab le fichier `docker-compose.yml` a été légèrement modifié : on map désormais le port `8081` de notre machine locale avec le port `80` du container `ansible-web`. Il faut donc bien lancer la commande `docker compose` depuis ce sous-répertoire `lab3` afin de bénéficier de ce nouveau mapping.

### Objectif

Écrire du code idempotent et dynamique.

## Tâches à réaliser

1. Rédiger un Playbook `site.yml` qui installe Nginx sur le(s) host(s) du groupe web.
    - Tip1: Nginx est installable directement via un package !
    - Tip2: il existe plusieurs modules Ansible qui permettent d'installer des package. Soit un module relatif à l'OS, soit un module générique. Ici en pratique, **utilisez le module relatif à l'OS** (l'utilisation du module générique sera moins simple, je vous montrerai pourquoi à la correction).
    - Tip3: ici votre "host" cible est en réalité un container, considérez que vous n'avez pas d'index de package. Vous devez ainsi spécifier au module Ansible de MAJ l'index des packages (le module doit lancer un `apt-get update` pour rafraîchir l'index).

2. Utiliser le module `template` pour générer un fichier `index.html` personnalisé avec le nom de l'hôte (tel qu'il apparaît dans l'inventaire).
    - Tip1: nous n'avons pas encore vu les rôles, pour l'instant on peut donc créer un répertoire `templates` au même niveau que le playbook `site.yml` afin d'y inclure nos templates jinja (que nous "templatiserons" depuis le playbook).
    - Tip2: le contenu du template jinja lui-même sera ici du bête html, contenant la variable Ansible que nous souhaitons afficher. Pour vous faciliter la tâche, vous pouvez partir de cet exemple:
    ```html
    <!DOCTYPE html>
    <html lang="fr">
    <head>
        <meta charset="UTF-8">
        <title>Lab 3 - Exemple de index.html Nginx minimaliste</title>
    </head>
    <body>

        <h1>Page générée par Ansible</h1>
        <p>Le playbook a correctement configuré ce serveur.</p>
        
        <hr>

        <p>Nom de la machine cible tel qu'il apparaît dans l'inventaire (magic variable Ansible ; nous les aborderons lors de la prochaine session) : <b>{{ inventory_hostname }}</b></p>

    </body>
    </html>
    ```

    - Tip3: le user/group par défaut d'Apache Nginx à son installation est `www-data`. Il faut que le fichier templatisé appartienne à ce user/group pour éviter les erreurs de permission (le fameux "HTTP 403 Forbidden").
    - Tip4: par défaut, Nginx chargera sa page par défaut depuis `/var/www/html/index.html`
    - Tip5: une fois déployé, la page devrait être accessible depuis votre navigateur via `http://localhost:8081/`

3. Ajouter un Handler pour redémarrer Nginx uniquement si la configuration est modifiée.

4. Ajouter une boucle loop pour installer une liste d'outils de base (vim, git, htop).


## Résultats attendus

- Playbook `site.yml`
  - contenu répondant aux attentes ci-dessus
  - exécution idempotente
- Template jinja
  - correctement placé dans l'arborescence du projet, correctement nommé
  - contenu correct (utilisation de la bonne variable)
- La page `http://localhost:8081/` affiche le nom du "host" dans l'inventaire, c'est à dire `target-web`
