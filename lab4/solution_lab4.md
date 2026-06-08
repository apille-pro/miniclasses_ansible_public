# solution_lab4.md

1. Refactoring du code en un rôle `nginx` :
    - Cf. contenu du rôle `nginx` et du playbook `site.yml` de ce lab4

2. Déplacement des playbooks :
    - Playbooks dépacés dans un répertoire `playbooks` créé à la racine (du lab4)
    - Ajout de la directive `roles_path = ./roles` dans la section `[defaults]` de `ansible.cfg`

3. Création d'un fichier `credentials.yml` chiffré avec Ansible Vault
    - Lancer la commande `ansible-vault create credentials.yml`
    - Fournir un password de chiffrement du fichier (a.k.a. un `Vault password`) ; dans cet exemple j'ai utilisé un `Vault password` très simple : `lab4`
        - Rappel : ne **JAMAIS** gitter un secret. Là il s'agit d'un exemple de chiffrement, où le mdp du fichier ne donne accès à aucun secret réel (c'est donc ok).
        - Sauvegardez votre `Vault password` dans un KeePass ou dans un Secret Manager (type Hashicorp Vault) ; il n'est pas suffisant de le sauvegarder dans un fichier local (type `.vault_pass`)
    - Saisir :
    ```yaml
    ---
    bdd_password: <mocked_mdp>
    ```

4. Lancement du déploiement (depuis le sous répertoire `lab4`) permettant d'afficher le mdp:
    - Ajouter une tâche de debug dans le playbook `site.yml` (cf. playbook)
    - Lancer `ansible-playbook -i inventories/hosts.ini playbooks/site.yml -e "@credentials.yml" --ask-vault-pass`
        - Nota: on aurait aussi pu utiliser un `--vault-password-file .vault_pass` à la place de `--ask-vault-pass`. Dans ce cas, créer le fichier `.vault_pass` et bien gérer le `.gitignore` du projet !!

5. Lancement du déploiement avec une extra-var contenant le nouveau path souhaité :
    - `ansible-playbook -i inventories/hosts.ini playbooks/site.yml -e 'nginx_www_location=/var/www/html/index_debug.html' -e "@credentials.yml" --ask-vault-pass`
    - Vérification de l'existence de ce nouveau fichier : `docker exec ansible-web ls -lh /var/www/html/`
