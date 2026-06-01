# README_lab4

## A vous de jouer !

- Placez-vous dans ce sous-répertoire `lab4` et remettez UP vos containers (`docker compose up -d`).

### Objectif

Transformer le playbook monolithique en un composant réutilisable et sécurisé.

## Tâches à réaliser

1. Déplacer toute la logique Nginx vers un rôle structuré `nginx`.
    - Créer les répertoires et fichiers nécessaires
    - Variabiliser ce qui doit l'être.
        - Tip1: pour la suite, variabilisez bien le path depuis lequel Nginx tente de charger sa "landing page"
        - Tip2: penser aux bonnes pratiques de variabilisation dans les rôles
    - Adapter le playbook `site.yml` afin qu'il utilise désormais ce rôle

2. Déplacer les playbooks `site.yml` et `test_connection.yml` dans un répertoire dédié aux playbooks.
    - Adapter la configuration Ansible en conséquence

3. Créer un fichier `credentials.yml` chiffré avec Ansible Vault
    - le fichier contiendra un "mocked" mdp de la BDD, dans une variable `bdd_password` (renseignez la valeur que vous souhaitez)

4. Afficher le mdp `bdd_password` depuis le playbook `site.yml`
    - Nota: il s'agit d'un lab, "dans la vrai vie" on ne fera jamais ça !!

5. Debug : 
    - Situation : *"Pour un besoin de debug exceptionnel, vous souhaitez configurer Nginx afin que sa page de démarrage soit templatisée à un path différent du path actuel"*
    - Comment procédez vous ?
        - Tip : ne PAS modifier le code.


## Résultats attendus

1. Refactoring du code en un rôle `nginx`
    - Rôle bien placé dans l'arborescence du projet
    - Organisation de ses sous-répertoires/fichiers OK et exhaustive (pour notre besoin)
    - Variabilisation correctement réalisée
    - Playbook `site.yml` correctement adapté

2. Emplacement des playbooks correctement modifié, config Ansible correctement adaptée

3. Fichier `credentials.yml` créé et chiffré avec Ansible Vault

4. Playbook `site.yml` correctement adapté afin d'afficher le mdp `bdd_password`

5. Approche correcte/adaptée trouvée
