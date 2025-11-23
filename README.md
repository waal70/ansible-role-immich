# ansible-role-immich

=======================
This role configs all aspects of immich.
To restore from a backup, perform the following actions:

## Backup and restore

* Make sure you have a backup of the 'library' folder inside ```immich_media_root```
* If you wish to keep the thumbnails and transcoded videos, also backup ```encoded-video``` and ```thumbs```
* If you do not have these, your new server instance will need to re-generate them, so backing them up will save time
* Make sure you have a current backup of the database. By default, they will be in ```backups```
* If needed (and/or possible), you can trigger a database dump in the admin job status page. Visit the admin page and create a new job
* Prepare shell access to the server that will hold your restored immich

## Performing the actual restore with aid of this role

* Install immich through this role onto a fresh server instance.
* Stop all containers of the stack in Portainer. Do not stop the stack as your containers will disappear!
* Copy over the backed up files to the ```immich_media_root```
* Nuke the DB-files from the initial start of the stack. ```rm -rf /data/immich/pgdata/```
* Start only the database container by selecting that container in Portainer and starting it. Wait until it registers 'healthy'
* In your shell, move to the folder containing your database backup ```cd immich_media_root\backups```
* Now perform (noting the filename should be your filename, and dbname and username should reflect the immich.yml.j2):

```bash
gunzip --stdout "immich-db-backup-20251123T020000-v2.3.1-pg14.19.sql.gz" \
| sed "s/SELECT pg_catalog.set_config('search_path', '', false);/SELECT pg_catalog.set_config('search_path', 'public, pg_catalog', true);/g" \
| docker exec -i immich_postgres psql --dbname=postgres --username=postgres
```

Be patient for a while, as the screen will flash ```ALTER TABLE``` and ```CREATE INDEX``` and the likes.

Now start the other containers. Once they are all 'healthy', go ahead and login to your new and fresh Immich!

## Tasks in Role

Work in progress!

## Requirements

- Debian-based installation of immich, through docker compose (a compose-file is provided in the project)

## Role Vars

*See main.yml under 'defaults' for a complete list and more description*

# Running the role

```

```

## Playbook example

---

```
- hosts: myhosts
  become: true
  vars:
    pip_install_packages:
      - name: docker
  vars_files:
    - vars/portainer.yml
  roles:
   - geerlingguy.docker
   - geerlingguy.pip
   - waal70.paperless-ngx
```

## License

-------

[GPLv3](https://www.gnu.org/licenses/gpl-3.0.html#license-text)

## Author Information

-------

Unless otherwise noted, this entire repository is (c) 2025 by André (waal70). [See github profile](https://github.com/waal70)

Please contact me if you need a commercial license for any of these files
