# solution_lab5.md

1. `ansible-lint` 
    - Commencer par installer ansible-lint : `pipx inject --include-apps ansible ansible-lint`
`ansible-lint site.yml`
    - Lancer `ansible-lint playbooks/site.yml`
        - Détecter "l'erreur" `name[casing]: All names should start with an uppercase letter. playbooks/site.yml:2:9`
        - La corriger (commencer le nom de la task par une majuscule : `Lab 3 [...]`)
        - Relancer le lint, ne plus trouver d'erreur

2. Import et utilisation d'un rôle communautaire via `ansible-galaxy`
    - Créer le fichier `requirements.yml` + le complèter avec le rôle souhaité (`geerlingguy.mysql` version `6.3.1`)  => cf. contenu de `requirements.yml`
    - Lancer l'installation des requirements : `ansible-galaxy install -r requirements.yml`
        - Nota : ce nouveau rôle apparait désormais à côté de notre rôle `nginx`, càd au path `lab5/roles/geerlingguy.mysql`
    - Ajouter un play au playbook `site.yml`, incluant la configuration et les variables souhaitées  => cf. contenu de `site.yml`

3. Déploiement complet : `ansible-playbook -i inventories/hosts.ini playbooks/site.yml`

4. Vérification `docker exec -it ansible-db mysql -u app_user -papp_lab_password -e "SHOW DATABASES;"`  => le retour affiche un tableau contenant notre bdd `miniclasses_db` (ainsi que `information_schema`). Ceci confirme que la stack est fonctionnelle et prête à être utilisée par de futures applications.
