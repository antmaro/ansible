# Some notes about Ansible

## BEST PRACTICES (source https://www.ansible.com/blog/ansible-best-practices-essentials)

### 1. Name your plays and Tasks
Always name you plays and tasks, for example:

```yaml
- hosts: web
  name: install and start apache
  taks:
    - name: install apache
      yum:
        name: httpd
        state: latest
```

### 2. Use Prefixes and Human Meaningful Names with Variables
The recommendation is prefix variables with the source or target of the data it represents. Prefixing variable is particulary vital with developing reusable and portables roles.

```yaml
apache_max_keepalive: 25
apache_port: 80
ssh_port: 22
```

### 3. Use Native YAML Syntax
Use native YAML for best readability, the native YAML has more lines but those lines are shorter reducing horizontal scrolling and line wrapping.

### 4. Use Modules before Run Commands
Don not abuse of command, shell or raw modules. Use the Ansible modules to perform de tasks because they are idempotent.

### 5. Clean Up your debuging messages
In the past, you had to delete or comment out your debug tasks and that was suboptimal approach though.

Starting in 2.1, the Ansible debug moduel supports a verbosity parameter that suppresses output unless the play is being run with a sufficiently high enough verbosity level.

```yaml
- debug:
  msg: "This always is displayed"

- debug:
  msg: "This only displays with ansible-playbook -vv+"
  verbosity: 2
```


## EXECUTIONS
Execute ansible ad-hoc in several hosts without inventory:

`$ ansible all -i host1.org, -m shell -a 'uptime' -u user1 -k`
