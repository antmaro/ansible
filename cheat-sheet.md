# Some notes about Ansible

## BEST PRACTICES

### 1. Name your plays and Tasks
Always name you plays and tasks, for example:

```yaml
- hosts: web
  name: install and start apache
  taks:
    - name: install apache

```

## EXECUTIONS
Execute ansible ad-hoc in several hosts without inventory:

`$ ansible all -i host1.org, -m shell -a 'uptime' -u user1 -k`
