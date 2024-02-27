# otus-proj-automatization

## Это Ansible Playbooks для автоматизации резервного копирования и развертывания проектной работы по курсу "Расширенное администрирование РЕД ОС" от OTUS.
## Для запуска РК текущего проекта вместе с docker images необходимыми для развертывания:

```sh
ansible-playbook backup_playbook.yml -k
```
Проверка создания РК:
```sh
09:08 $ ansible-playbook backup_playbook.yml --tags "check" --vault-id artys@~/ansible-vault.pass

PLAY [Create Backup of current state of project] ********************************************************************************************************************************************************************************

TASK [Check backups are crated in systemctl] ************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 192.168.88.37 is using the discovered Python interpreter at /usr/bin/python3.8, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.15/reference_appendices/interpreter_discovery.html for more information.
changed: [192.168.88.37]

TASK [Debug registered var] *****************************************************************************************************************************************************************************************************
ok: [192.168.88.37] => {
    "result.stdout_lines": [
        "total 3.6G",
        "drwxrwxr-x.  2 artys artys 4.0K Feb 26 17:08 .",
        "drwx------. 21 artys artys 4.0K Feb 26 17:50 ..",
        "-rw-rw-r--.  1 artys artys 1.3G Feb 26 12:34 elasticsearch.tar",
        "-rw-rw-r--.  1 artys artys 314M Feb 26 12:35 filebeat.tar",
        "-rw-rw-r--.  1 artys artys 1.1G Feb 26 12:33 kibana.tar",
        "-rw-rw-r--.  1 artys artys 183M Feb 26 12:32 nginx.tar",
        "-rw-rw-r--.  1 artys artys 364M Feb 26 16:53 otus-proj.tgz",
        "-rw-rw-r--.  1 artys artys 367M Feb 27 08:38 volumes.tgz"
    ]
}

PLAY RECAP **********************************************************************************************************************************************************************************************************************
192.168.88.37              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
## Для восстановления РК проекта на другой машине:
```sh
ansible-playbook restore_playbook.yml -K
```
Пример выполнения:
```sh
08:56 $ ansible-playbook restore_playbook.yml --tags "test" --vault-id artys@~/ansible-vault.pass

PLAY [Config new machine] *******************************************************************************************************************************************************************************************************

TASK [Check containers are up] **************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 192.168.88.38 is using the discovered Python interpreter at /usr/bin/python3.8, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.15/reference_appendices/interpreter_discovery.html for more information.
changed: [192.168.88.38]

TASK [Debug registered var] *****************************************************************************************************************************************************************************************************
ok: [192.168.88.38] => {
    "result.stdout_lines": [
        "CONTAINER ID   IMAGE                                                  COMMAND                  CREATED          STATUS                    PORTS                           NAMES",
        "8d8668982327   nginx:latest                                           \"/docker-entrypoint.…\"   2 minutes ago    Up 44 seconds (healthy)   80/tcp, 0.0.0.0:8443->443/tcp   nf-nginx",
        "3d13e1a4a33e   docker.elastic.co/beats/filebeat:8.12.1                \"/usr/bin/tini -- /u…\"   2 minutes ago    Up 56 seconds (healthy)                                   nf-filebeat",
        "34cade5114d0   docker.elastic.co/kibana/kibana:8.12.1                 \"/bin/tini -- /usr/l…\"   2 minutes ago    Up 2 minutes (healthy)    5601/tcp                        nf-kibana",
        "4781bd0281ba   docker.elastic.co/elasticsearch/elasticsearch:8.12.1   \"/bin/tini -- /usr/l…\"   44 minutes ago   Up 44 minutes (healthy)   9200/tcp, 9300/tcp              nf-elastic"
    ]
}

TASK [Check service status in systemctl] ****************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Debug registered var] *****************************************************************************************************************************************************************************************************
ok: [192.168.88.38] => {
    "result.stdout_lines": [
        "● nf-app.service - NF-Collector",
        "     Loaded: loaded (/etc/systemd/system/nf-app.service; enabled; vendor preset: disabled)",
        "     Active: active (exited) since Tue 2024-02-27 09:00:43 MSK; 47s ago",
        "    Process: 18539 ExecStart=/usr/bin/docker-compose up -d (code=exited, status=0/SUCCESS)",
        "   Main PID: 18539 (code=exited, status=0/SUCCESS)",
        "        CPU: 961ms",
        "",
        "Feb 27 09:00:31 otus-proj-new.lab.local docker-compose[18565]: Container nf-filebeat  Started",
        "Feb 27 09:00:31 otus-proj-new.lab.local docker-compose[18565]: Container nf-filebeat  Waiting",
        "Feb 27 09:00:31 otus-proj-new.lab.local docker-compose[18565]: Container nf-elastic  Waiting",
        "Feb 27 09:00:31 otus-proj-new.lab.local docker-compose[18565]: Container nf-kibana  Waiting",
        "Feb 27 09:00:32 otus-proj-new.lab.local docker-compose[18565]: Container nf-elastic  Healthy",
        "Feb 27 09:00:32 otus-proj-new.lab.local docker-compose[18565]: Container nf-kibana  Healthy",
        "Feb 27 09:00:42 otus-proj-new.lab.local docker-compose[18565]: Container nf-filebeat  Healthy",
        "Feb 27 09:00:42 otus-proj-new.lab.local docker-compose[18565]: Container nf-nginx  Starting",
        "Feb 27 09:00:43 otus-proj-new.lab.local docker-compose[18565]: Container nf-nginx  Started",
        "Feb 27 09:00:43 otus-proj-new.lab.local systemd[1]: Finished NF-Collector."
    ]
}

PLAY RECAP **********************************************************************************************************************************************************************************************************************
192.168.88.38              : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
## Для создания РК только volumes контейнеров (nginx с SSL-сертификатами и хранилище Elasticsearch):
```sh
ansible-playbook backup_only_volumes.yml --tags "test" --vault-id artys@~/ansible-vault.pass
```
```sh
08:20 $ ansible-playbook backup_only_volumes.yml --tags "test" --vault-id artys@~/ansible-vault.pass

PLAY [Create Backup of current state of project] ********************************************************************************************************************************************************************************

TASK [Create Backup of Volumes Project state] ***********************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 192.168.88.37 is using the discovered Python interpreter at /usr/bin/python3.8, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.15/reference_appendices/interpreter_discovery.html for more information.
changed: [192.168.88.37]

TASK [Copy archieve with Project Volumes to this host] **************************************************************************************************************************************************************************
changed: [192.168.88.37]

PLAY RECAP **********************************************************************************************************************************************************************************************************************
192.168.88.37              : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
```sh
✘-2 ~/ansible [main|● 1✚ 1…1] 
08:24 $ ls -lah  backups/192.168.88.37/home/artys/backups/ | grep volumes.tgz
-rw-r--r-- 1 artys artys 367M Feb 27 08:23 volumes.tgz
```