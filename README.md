# otus-proj-automatization

## Это Ansible Playbooks для автоматизации резервного копирования и развертывания на другой машине проектной работы по курсу "Расширенное администрирование РЕД ОС" от OTUS.
## Для запуска РК текущего проекта вместе с docker images необходимыми для развертывания на другой машине:

```sh
ansible-playbook backup_playbook.yml -k
```
## Для восстановления РК проекта на другой машине:
```sh
ansible-playbook restore_playbook.yml -K
```
