# README_lab2

## A vous de jouer !

- Commencez par remettre UP vos containers (en vous plaçant dans le répertoire `lab1` + `docker compose up -d`)
- Puis placez vous dans ce répertoire `lab2` pour réaliser la suite

### Objectif

Maîtriser l'organisation des hôtes et la séparation des données via les inventaires structurés.

## Tâches à réaliser

1. Créer un fichier d'inventaire incluant les 2 managed nodes `target-web` et `target-db`, répartis dans les 2 groupes `web` (1 hôte) et `db` (1 hôte). Appliquez directement dans cet inventaire les variables `ansible_host=127.0.0.1` et `ansible_port=<2221 pour target-web ou 2222 pour target-db>` aux managed nodes.
    - Nota1: le fichier d'inventaire a déjà été créé pour vous dans le lab1, néanmoins essayez de le recréer seul (au format INI ou YAML)
    - Nota2: appliquez l'arborescence Ansible de type "Alternative directory layout"

2. Créer un groupe parent `production` englobant les deux groupes précédents (en INI ou en YAML)

3. Mettre en place la variable `env: prod` dans les group_vars (répertoire à créer !) afin que la variable s'applique à tous les hosts contenus dans l'inventaire

4. Valider la hiérarchie avec la commande : `ansible-inventory --graph`  (tip : il manque un élément dans cette commande !)

5. BONUS: bien que nous n'ayons pas encore vu les playbook, vous pourriez copier-coller `lab1/test_connection.yml` dans ce répertoire puis essayer de lui ajouter une tâche afin d'afficher le contenu de la variable `env` *(tip:  `"toto={{ toto }}"`)*.

## Résultats attendus

1. à 3. Structure de répertoires + fichiers + contenus corrects

4. Output attendu:
```bash
@all:
  |--@ungrouped:
  |--@production:
  |  |--@web:
  |  |  |--target-web
  |  |--@db:
  |  |  |--target-db
``` 

5. Playbook contenant une nouvelle task affichant le contenu de la variable `env`