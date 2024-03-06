# otus-proj-automatization

## Это Ansible Playbooks для автоматизации резервного копирования и развертывания проектной работы по курсу "Расширенное администрирование РЕД ОС" от OTUS.
## Для запуска РК текущего проекта вместе с docker images необходимыми для развертывания:

```sh
ansible-playbook backup_playbook.yml -k
```
Проверка создания РК:
```sh
15:16 $ ansible-playbook backup_playbook.yml --vault-id artys@~/ansible-vault.pass

PLAY [Create Backup of current state of project] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host otusproj is using the discovered Python interpreter at /usr/bin/python3.8, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.15/reference_appendices/interpreter_discovery.html for more information.
ok: [otusproj]

TASK [Display the current timestamp in YYYY-MM-DD] **************************************************************************************************************************************************************************************
ok: [otusproj] => {
    "ansible_date_time.date": "2024-03-06"
}

TASK [Create Ansible dir for backups] ***************************************************************************************************************************************************************************************************
ok: [otusproj -> localhost]

TASK [Stop app from docker-compose] *****************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Create Backup dir] ****************************************************************************************************************************************************************************************************************
ok: [otusproj]

TASK [Create Backup dir] ****************************************************************************************************************************************************************************************************************
ok: [otusproj]

TASK [Create Backup of current Project state] *******************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Export all project images] ********************************************************************************************************************************************************************************************************
ok: [otusproj]

TASK [Check backups are created] ********************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Debug registered var] *************************************************************************************************************************************************************************************************************
ok: [otusproj] => {
    "result.stdout_lines": [
        "total 614M",
        "drwxrwxr-x. 2 artys artys 4.0K Mar  6 14:50 .",
        "drwxrwxr-x. 5 artys artys 4.0K Mar  6 14:50 ..",
        "-rw-rw-r--. 1 artys artys 614M Mar  6 15:19 otus-proj.tgz"
    ]
}

TASK [Copy all images to this host] *****************************************************************************************************************************************************************************************************
ok: [otusproj]

TASK [Copy project to this host] ********************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Run app from docker-compose] ******************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Debug registered var] *************************************************************************************************************************************************************************************************************
ok: [otusproj] => {
    "result.stdout_lines": "VARIABLE IS NOT DEFINED!"
}

TASK [Check containers are up] **********************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Debug registered var] *************************************************************************************************************************************************************************************************************
ok: [otusproj] => {
    "result.stdout_lines": [
        "CONTAINER ID   IMAGE                                                  COMMAND                  CREATED         STATUS                            PORTS                           NAMES",
        "a1387d9687ec   nginx:latest                                           \"/docker-entrypoint.…\"   2 minutes ago   Up 3 seconds (health: starting)   80/tcp, 0.0.0.0:8443->443/tcp   nf-nginx",
        "64e9973a3c9a   docker.elastic.co/beats/filebeat:8.12.1                \"/usr/bin/tini -- /u…\"   2 minutes ago   Up 15 seconds (healthy)                                           nf-filebeat",
        "5d9ed256e798   docker.elastic.co/kibana/kibana:8.12.1                 \"/bin/tini -- /usr/l…\"   2 minutes ago   Up About a minute (healthy)       5601/tcp                        nf-kibana",
        "2dd09a1a3a2f   docker.elastic.co/elasticsearch/elasticsearch:8.12.1   \"/bin/tini -- /usr/l…\"   2 minutes ago   Up 2 minutes (healthy)            9200/tcp, 9300/tcp              nf-elastic"
    ]
}

PLAY RECAP ******************************************************************************************************************************************************************************************************************************
otusproj                   : ok=16   changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
## Для восстановления РК проекта на другой машине:
```sh
ansible-playbook restore_playbook.yml -K
```
Пример выполнения:
```sh
✔ ~/ansible [main|✚ 5…1] 
16:15 $ ansible-playbook restore_playbook.yml --vault-id artys@~/ansible-vault.pass

PLAY [Config new machine] ***************************************************************************************************************************************************************************************************************

TASK [Copy docker-repo to yum.repos.d] **************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host otusproj is using the discovered Python interpreter at /usr/bin/python3.11, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.15/reference_appendices/interpreter_discovery.html for more information.
changed: [otusproj]

TASK [DNF Update] ***********************************************************************************************************************************************************************************************************************
ok: [otusproj]

TASK [Add env for docker] ***************************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Check env for docker] *************************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Upgrade all packages] *************************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Install Docker, Docker-Compose, Cockpit] ******************************************************************************************************************************************************************************************
ok: [otusproj] => (item=cockpit)
changed: [otusproj] => (item=cockpit-machines)
ok: [otusproj] => (item=cockpit-storaged)
changed: [otusproj] => (item=cockpit-pcp)
changed: [otusproj] => (item=docker-ce)
ok: [otusproj] => (item=docker-ce-cli)
changed: [otusproj] => (item=docker-compose-plugin)

TASK [Copy cockpit-docker.tar.gz] *******************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Unarchive cockpit-docker.tar.gz] **************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [List installed packages] **********************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [debug] ****************************************************************************************************************************************************************************************************************************
ok: [otusproj] => {
    "result.stdout_lines": [
        "cockpit.x86_64                                    299-2.red80                           @anaconda        ",
        "cockpit-bridge.x86_64                             299-2.red80                           @anaconda        ",
        "cockpit-machines.noarch                           252-3.red80                           @base            ",
        "cockpit-networkmanager.noarch                     299-2.red80                           @anaconda        ",
        "cockpit-packagekit.noarch                         299-2.red80                           @anaconda        ",
        "cockpit-pcp.x86_64                                299-2.red80                           @base            ",
        "cockpit-selinux.noarch                            299-2.red80                           @anaconda        ",
        "cockpit-storaged.noarch                           299-2.red80                           @anaconda        ",
        "cockpit-system.noarch                             299-2.red80                           @anaconda        ",
        "cockpit-ws.x86_64                                 299-2.red80                           @anaconda        ",
        "containerd.io.x86_64                              1.6.28-3.1.el7                        @docker-ce-stable",
        "docker-ce.x86_64                                  4:24.0.7-1.red80                      @base            ",
        "docker-ce-cli.x86_64                              4:24.0.7-1.red80                      @base            ",
        "docker-compose-plugin.x86_64                      2.24.6-1.el7                          @docker-ce-stable"
    ]
}

TASK [Enable & Start cockpit.socket] ****************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Enable & Start pmlogger.service] **************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Enable & Start docker] ************************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Create dir to import Backups] *****************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Copy Images Backup to New Host] ***************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Copy Project Backup to New Host] **************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [add user to docker group] *********************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Reset connection to reapply groups to user] ***************************************************************************************************************************************************************************************

TASK [Load all image(s) from the given tar files] ***************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [debug] ****************************************************************************************************************************************************************************************************************************
ok: [otusproj] => {
    "result.stdout_lines": [
        "Loaded image: nginx:latest",
        "Loaded image: docker.elastic.co/elasticsearch/elasticsearch:8.12.1",
        "Loaded image: docker.elastic.co/kibana/kibana:8.12.1",
        "Loaded image: docker.elastic.co/beats/filebeat:8.12.1"
    ]
}

TASK [Create docker dir in home] ********************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Unarchive project to this directory] **********************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Disable IPv6 with sysctl] *********************************************************************************************************************************************************************************************************
changed: [otusproj] => (item=net.ipv6.conf.all.disable_ipv6)
changed: [otusproj] => (item=net.ipv6.conf.default.disable_ipv6)
changed: [otusproj] => (item=net.ipv6.conf.lo.disable_ipv6)

TASK [Run init.sh to change MAIN_IP and DNS_IP in .env] *********************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Run app from docker-compose] ******************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [Check containers are up] **********************************************************************************************************************************************************************************************************
changed: [otusproj]

TASK [debug] ****************************************************************************************************************************************************************************************************************************
ok: [otusproj] => {
    "result.stdout_lines": [
        "CONTAINER ID   IMAGE                                                  COMMAND                  CREATED         STATUS                            PORTS                           NAMES",
        "d7fad607eadb   nginx:latest                                           \"/docker-entrypoint.…\"   2 minutes ago   Up 2 seconds (health: starting)   80/tcp, 0.0.0.0:8443->443/tcp   nf-nginx",
        "fd9335cd626e   docker.elastic.co/beats/filebeat:8.12.1                \"/usr/bin/tini -- /u…\"   2 minutes ago   Up 14 seconds (healthy)                                           nf-filebeat",
        "d16721d0208e   docker.elastic.co/kibana/kibana:8.12.1                 \"/bin/tini -- /usr/l…\"   2 minutes ago   Up About a minute (healthy)       5601/tcp                        nf-kibana",
        "111676c4cf7d   docker.elastic.co/elasticsearch/elasticsearch:8.12.1   \"/bin/tini -- /usr/l…\"   2 minutes ago   Up 2 minutes (healthy)            9200/tcp, 9300/tcp              nf-elastic"
    ]
}

PLAY RECAP ******************************************************************************************************************************************************************************************************************************
otusproj                   : ok=26   changed=22   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

✔ ~/ansible [main|✚ 5…1] 
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