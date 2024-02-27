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
14:01 $ ansible-playbook restore_playbook.yml --vault-id artys@~/ansible-vault.pass

PLAY [Config new machine] *******************************************************************************************************************************************************************************************************

TASK [DNF Update] ***************************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 192.168.88.38 is using the discovered Python interpreter at /usr/bin/python3.8, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.15/reference_appendices/interpreter_discovery.html for more information.
ok: [192.168.88.38]

TASK [Upgrade all packages] *****************************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Install Docker, Docker-Compose, Cockpit] **********************************************************************************************************************************************************************************
changed: [192.168.88.38] => (item=cockpit)
changed: [192.168.88.38] => (item=cockpit-machines)
changed: [192.168.88.38] => (item=cockpit-storaged)
changed: [192.168.88.38] => (item=docker-ce)
changed: [192.168.88.38] => (item=docker-ce-cli)
changed: [192.168.88.38] => (item=docker-compose)

TASK [Copy cockpit-docker.tar.gz] ***********************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Unarchive cockpit-docker.tar.gz] ******************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Enable & Start cockpit.socket] ********************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Enable & Start docker] ****************************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Create Backup dir] ********************************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Create Log dir] ******************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Copy Images and Project Backup to New Host] **************************************************************************************************************************************************************
changed: [192.168.88.38] => (item=kibana.tar)
changed: [192.168.88.38] => (item=elasticsearch.tar)
changed: [192.168.88.38] => (item=nginx.tar)
changed: [192.168.88.38] => (item=filebeat.tar)
changed: [192.168.88.38] => (item=otus-proj.tgz)

TASK [add user 'artys' to group docker] *****************************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Load Elastic image from tarball] *************************************************************************************************************************************************************************
changed: [192.168.88.38] => (item={'name': 'docker.elastic.co/elasticsearch/elasticsearch', 'tag': '8.12.1', 'path': 'elasticsearch.tar'})
changed: [192.168.88.38] => (item={'name': 'docker.elastic.co/kibana/kibana', 'tag': '8.12.1', 'path': 'kibana.tar'})
changed: [192.168.88.38] => (item={'name': 'docker.elastic.co/beats/filebeat', 'tag': '8.12.1', 'path': 'filebeat.tar'})
changed: [192.168.88.38] => (item={'name': 'nginx', 'tag': 'latest', 'path': 'nginx.tar'})

TASK [Create docker dir in home] *******************************************************************************************************************************************************************************
changed: [192.168.88.38]

ASK [Unarchive project to same directory] *********************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Disable IPv6 with sysctl] ********************************************************************************************************************************************************************************
changed: [192.168.88.38] => (item=net.ipv6.conf.all.disable_ipv6)
changed: [192.168.88.38] => (item=net.ipv6.conf.default.disable_ipv6)
changed: [192.168.88.38] => (item=net.ipv6.conf.lo.disable_ipv6)

TASK [Run init.sh to change MAIN_IP and DNS_IP in .env] ********************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Copy .service to /etc/systemd/system/] *******************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Enable & Start nf-app] ***********************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Check containers are up] *********************************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Try to see 'docker ps -a'] *******************************************************************************************************************************************************************************
ok: [192.168.88.38] => {
    "result.stdout_lines": [
        "CONTAINER ID   IMAGE                                                  COMMAND                  CREATED          STATUS                            PORTS                           NAMES",
        "b1c95af94d90   nginx:latest                                           \"/docker-entrypoint.…\"   18 minutes ago   Up 2 seconds (health: starting)   80/tcp, 0.0.0.0:8443->443/tcp   nf-nginx",
        "ac990a0365db   docker.elastic.co/beats/filebeat:8.12.1                \"/usr/bin/tini -- /u…\"   18 minutes ago   Up 15 seconds (healthy)                                           nf-filebeat",
        "015550bd828e   docker.elastic.co/kibana/kibana:8.12.1                 \"/bin/tini -- /usr/l…\"   18 minutes ago   Up About a minute (healthy)       5601/tcp                        nf-kibana",
        "57534b23de7c   docker.elastic.co/elasticsearch/elasticsearch:8.12.1   \"/bin/tini -- /usr/l…\"   18 minutes ago   Up 2 minutes (healthy)            9200/tcp, 9300/tcp              nf-elastic"
    ]
}

TASK [Check service status in systemctl] ***********************************************************************************************************************************************************************
changed: [192.168.88.38]

TASK [Try to see 'systemctl status nf-app'] ********************************************************************************************************************************************************************
ok: [192.168.88.38] => {
    "result.stdout_lines": [
        "● nf-app.service - NF-Collector",
        "     Loaded: loaded (/etc/systemd/system/nf-app.service; enabled; vendor preset: disabled)",
        "     Active: active (exited) since Tue 2024-02-27 16:01:38 MSK; 5s ago",
        "    Process: 110849 ExecStart=/usr/bin/docker-compose up -d (code=exited, status=0/SUCCESS)",
        "   Main PID: 110849 (code=exited, status=0/SUCCESS)",
        "        CPU: 1.456s",
        "",
        "Feb 27 16:01:26 otus-proj-new.lab.local docker-compose[110873]: Container nf-filebeat  Started",
        "Feb 27 16:01:26 otus-proj-new.lab.local docker-compose[110873]: Container nf-filebeat  Waiting",
        "Feb 27 16:01:26 otus-proj-new.lab.local docker-compose[110873]: Container nf-elastic  Waiting",
        "Feb 27 16:01:26 otus-proj-new.lab.local docker-compose[110873]: Container nf-kibana  Waiting",
        "Feb 27 16:01:26 otus-proj-new.lab.local docker-compose[110873]: Container nf-elastic  Healthy",
        "Feb 27 16:01:26 otus-proj-new.lab.local docker-compose[110873]: Container nf-kibana  Healthy",
        "Feb 27 16:01:37 otus-proj-new.lab.local docker-compose[110873]: Container nf-filebeat  Healthy",
        "Feb 27 16:01:37 otus-proj-new.lab.local docker-compose[110873]: Container nf-nginx  Starting",
        "Feb 27 16:01:38 otus-proj-new.lab.local docker-compose[110873]: Container nf-nginx  Started",
        "Feb 27 16:01:38 otus-proj-new.lab.local systemd[1]: Finished NF-Collector."
    ]
}

PLAY RECAP *****************************************************************************************************************************************************************************************************
192.168.88.38              : ok=2   changed=25    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
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