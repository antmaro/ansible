# Project where I have some notes about Ansible

## EXECUTIONS
Execute ansible ad-hoc in several hosts without inventory:

`$ ansible all -i host1.org, -m shell -a 'uptime' -u user1 -k`
