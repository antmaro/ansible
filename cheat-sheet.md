# Some notes about Ansible
Antonio Mari, January 2018, v1.0 (for Ansible 2.2)

## BEST PRACTICES (source [best_practices](https://www.ansible.com/blog/ansible-best-practices-essentials))

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



## CONTROL AND MANAGED NODE REQUIREMENTS
[requirements](http://docs.ansible.com/ansible/latest/intro_installation.html#control-machine-requirements)

### CONTROL NODE
Ansible can be run from any machine with Python 2 (versions 2.6 or 2.7) or Python 3 (versions 3.5 and higher). Windows is not supported for control machine.

### MANAGED NODE
For this kind of nodes the following requirements are needed:

 * SSH (default uses sftp but you can switch to scp in ansible.cfg)
 * Python 2.6 or later**

** Ansible raw module and the script module do not even need python, so you can use ansible to install python-simplejson using the raw module, which then allows you to use everything else.
 * libselinux-python if you have SELinux enabled on remote nodes (to use copy/file/template functions in Ansible)


## Configuration File
[configuration_file.html](http://docs.ansible.com/ansible/latest/intro_configuration.html)

Use in order of preference:
 + Contents of `$ANSIBLE_CONFIG` environment variable
 + `./ansible.cfg`
 + `~/.ansible.cfg`
 + `/etc/ansible/ansible.cfg`

To see what will be the config used by ansible:
``` bash
   # ansible --version
     ansible 2.3.1.0
     config file = /tmp/ansible.cfg
```

## INVENTORY FILES
[intro_inventory.html](http://docs.ansible.com/ansible/latest/intro_inventory.html), [intro_dynamic_inventory.html](http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html)

Two kind of definitions for inventory file:

### INI-file structure: 
Blocks define groups. Hosts allowed in more than one group. 

Hostname ranges: `www[01:50].example.com`, `db-[a:f].example.com`

Per-host variables: `thor.example.com ansible_ssh_port=2424 variable1=var1 variable2=var2`

- `[foo:children]`: new group foo containing all members if included groups
- `[foo:vars]`: variable definition for all members of group foo

Inventory file defaults to `/etc/ansible/hosts`. Veritable with `-i` or in the configuration file. The file can also be a dynamic inventory script. If a directory, all contained files are processed.

### YAML file
Definition of inventory:
``` yaml
 all:
   hosts:
     mail.example.com
   children:
     webservers:
       hosts:
         foo.example.com:
         bar.example.com:
    dbservers:
       hosts:
         one.example.com:
         two.example.com:
         three.example.com:
```

Definition of variables for a host (or group)
``` yaml
hosts:
  jumper:
    ansible_port: 5555
    ansible_host: 192.0.2.50
```

## PATTERNS
[intro_patterns](http://docs.ansible.com/ansible/latest/intro_patterns.html)

Used on the `ansible` command line or in playbooks:

* `all` or `*`
* hostname: `lab.example.com`
* groupname: `webservers` or `webservers:dbservers`
* exclude: `webservers:!phoneix`
* intersection: `webservers:&staging`

You can do combinations: 
`websersers:dbservers:&staging:!phoneix`

You can also user variables if you want to pass some group specifiers via the "-e" argument to any ansible playbook:
`webservers:!{{excluded}}:&{{required}}`

Also you can use wildcards: `*.exampe.com` or 192.168.1.* and regular expressions: `~(web|db).*\.example\.com`

And finally you can refer to hosts within the group by adding a subscript to the group name. For instance if you have the following group:
``` yaml
[webservers]
web1
web2
web3
```

You can select a host or subset of hosts from a group by their position:
``` yaml
webservers[0]	# web1
webservers[0:1]	# web1,web2
webservers[1:]	# web2,web3
```

## Configuration VIM for YAML

These are my favourite options in .vimrc to edit yaml files for Ansible:

```bash
autocmd FileType yaml setlocal ai ts=2 sw=2 et nu cuc
autocmd FileTyep yaml colo desert
``

## Jinja2 Templates

- Jinja templates uses {% EXPR %} for expressions and logic
- And {{ }} for outputting the results and expressions.

For example:

{{ ansible_facts['default_ipv4']['address'] }}  {{ ansible_facts['hostname'] }}

### Control Structures

#### Using loops

In this example the *user* variable is replaced with all the values included in the *users* variable
``` yaml
{% for user in users %}
	{{ user }}
{% end for %}
```

=== Controling Task Execution

The order when the tasks are running:

1. pre_tasks
	|__ handler notification (pre_tasks)
2. roles
3. tasks
4. handlers notified by roles and tasks
4. post_tasks
	|__ handler by post_taks


==== import_ vs include_

include_role --> dynamically include a role
~~
Ansible parses and inserts the role in play when it reaches the include role

import_role --> statically import a role
~~~
Ansible parses the role at the begining and it detects errors before starting executing tasks.

:

	 
