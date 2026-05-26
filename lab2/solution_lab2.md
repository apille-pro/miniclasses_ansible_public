# solution_lab2.md

1. à 3. Structure de répertoires + fichiers + contenus corrects (cf. répertoire `inventories` de ce lab2)

4. Commande (+output):
```bash
$ ansible-inventory -i inventories/hosts.ini --graph
@all:
  |--@ungrouped:
  |--@production:
  |  |--@web:
  |  |  |--target-web
  |  |--@db:
  |  |  |--target-db
``` 

5. Cf. playbook `test_connection.yml` de ce lab2