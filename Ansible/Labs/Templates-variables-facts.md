# Templates, Variables and Facts Lab

- Deploy hardened sudoers file by using Ansible template.
- Must validate before deployment

### [Template](https://docs.ansible.com/ansible/latest/modules/template_module.html)

```jinja2
# /home/ansible/hardened.j2

%sysops {{ ansible_default_ipv4.address }} = (ALL) ALL
Host_Alias WEBSERVERS = {{ groups['web']|join(', ') }}
Host_Alias DBSERVERS = {{ groups['database']|join(', ') }} 
%httpd WEBSERVERS = /bin/su - webuser
%dba DBSERVERS = /bin/su - dbuser

```

### [Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html)

`‘hostvars’`, `‘group_names’`, and `‘groups’` .

{{ ansible_default_ipv4.address }} : get remote host IP

{{ groups['web']|join(', ') }} : return array style list and format to comma separated list using join() 

```
ansible web --list-hosts
ansible database --list-hosts
```

### Playbook

```yaml
---
- hosts: all
  become: yes
  tasks:
  - name: deploy sudo template
    template:
      src: /home/ansible/hardened.j2
      dest: /etc/sudoers.d/hardened
      validate: /sbin/visudo -cf %s
```

**Template - validate flag** to help validate sudo file before placing into sudoers.d.

**-cf [flag](https://www.sudo.ws/man/1.8.17/visudo.man.html)** - enable check only mode and specify alternate sudoer file location.

**The path to the file to validate is passed in via '%s' which must be present.**

[Reference](https://docs.ansible.com/ansible/latest/modules/template_module.html#examples)

**Generated file**

```
%sysops 192.456.654.987 = (ALL) ALL
Host_Alias WEBSERVERS = webserver1, webserver2
Host_Alias DBSERVERS = dbserver1, dbserver2
%httpd WEBSERVERS = /bin/su - webuser
%dba DBSERVERS = /bin/su - dbuser
```

