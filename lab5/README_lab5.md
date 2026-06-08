# README_lab5

## A vous de jouer !

- Placez-vous dans ce sous-répertoire `lab5` et remettez UP vos containers (`docker compose up -d`).

### Objectif

Finaliser le projet avec les standards professionnels.

## Tâches à réaliser

1. Passer le code au crible de `ansible-lint` et corriger toutes les alertes.
    - Tip1: dans le lab1 nous avions installé Ansible et ses dépendances via `pipx`. Pour autant, `ansible-lint` n'est pas une dépendance de Ansible, mais un projet ditinct reposant sur Ansible ! Bref, la bonne manière d'installer `ansible-lint` de manière isolée du système MAIS dans le même environnement virtuel que les commandes Ansible précédentes est: `pipx inject --include-apps ansible ansible-lint`.

2. Créer un fichier `requirements.yml` pour importer un rôle MySQL/MariaDB de la communauté (rôle `geerlingguy.mysql` sur ansible galaxy). Ce rôle permettra d'équiper les hosts du groupe `db` (de l'inventaire) de MariaDB (fork libre de MySQL) :
    - d'abord créer le fichier `requirements.yml` faisant référence au rôle `geerlingguy.mysql` (cf. https://galaxy.ansible.com/ui/standalone/roles/geerlingguy/mysql/)
        - tip : quand on importe un rôle de la communauté, outre les vérifications de sécurité, il faut aussi lire sa doc 😉
    - puis lancer l'installation des dépendances via `ansible-galaxy`
    - et enfin MAJ le playbook `site.yml` afin d'installer le rôle `geerlingguy.mysql` sur les hosts du groupe `db`.
        - Tip: dans ce playbook, simplement rajouter un play déployant le rôle `geerlingguy.mysql` sur les hosts du groupe `db`. Pour ce play :
            - activer le `gather_facts`
            - exécuter l'intégralité en `root`
        - Nota: en pratique nous allons installer MariaDB (et non MySQL) : MariaDB est libre et est en principe plus léger/performant pour un usage conteneurisé. En lisant la documentation du rôle, vous constatez que ce rôle est assez paramétrable. En pratique, vous allez vouloir lancer l'exécution du nouveau play avec les variables de play suivantes :
        ```yaml
        # 1. Forcer l'installation de MariaDB à la place de MySQL (plus adapté à Docker)
        mysql_packages:
        - mariadb-server
        - mariadb-client
        - python3-mysqldb  # Requis par Ansible pour exécuter les requêtes de création de users

        # 2. Indiquer au shim systemctl le nom exact du service MariaDB (spécifique à l'environnement de notre lab !)
        mysql_daemon: "mariadb"

        # 3. Sécurisation des accès globaux
        mysql_root_password: "root_lab_password"  # Exemple ; on pourrait ici utiliser un password protégé via Ansible-Vault par exemple
        mysql_bind_address: "0.0.0.0"  # Indispensable pour que le conteneur Web puisse interroger la DB

        # 4. Création automatique de la base de données "miniclasses_db" du lab
        mysql_databases:
        - name: "miniclasses_db"
            encoding: "utf8"
            collation: "utf8_general_ci"

        # 5. Création du user applicatif "app_user" (celui qu'utiliserait Nginx plus tard), en lui donnant tous les droits sur la bdd "miniclasses_db" 
        mysql_users:
        - name: "app_user"
            password: "app_lab_password"  # Exemple ; on pourrait ici utiliser un password protégé via Ansible-Vault par exemple
            host: "%"  # Le caractère '%' permet la connexion depuis n'importe quelle IP du réseau Docker
            priv: "miniclasses_db.*:ALL"
        ```


3. Lancer le déploiement complet, en exécution réelle.
    - Nota: le mode `--check` "doesn't play nice" avec l'installation et la vérification de la présence de paquets.

4. Vérifier que la base de données est accessible. Dans votre WSL exécutez : `docker exec -it ansible-db mysql -u app_user -papp_lab_password -e "SHOW DATABASES;"`
    - Nota: on se permet d'écrire le mdp ici en clair dans la commande car il s'agit d'un lab sans aucun accès réel / sans aucune donnée réelle ! A ne pas faire sinon !

## Résultats attendus

1. `ansible-lint` correctement installé, exécution ok après correction

2. `requirements.yml` correct, installation du rôle `geerlingguy.mysql` via `ansible-galaxy` ok, playbook `site.yml` correctement modifié (nouveau play, utilisant les configurations et variables souhaitées)

3. déploiement ok

4. vérification ok : le retour affiche un tableau contenant notre bdd `miniclasses_db` (ainsi que `information_schema`). Ceci confirme que la stack est fonctionnelle et prête à être utilisée par de futures applications.
