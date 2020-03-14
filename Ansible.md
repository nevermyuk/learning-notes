# Ansible

- Automation engine that allows for agentless system configuration and deployments
- Operates over SSH and runs Ansible modules on remote system in order to complete task
- Installed on a single control node where a list of host inventory files and playbooks are kept
- Heavy lifting performed on remote host, where Ansible Modules are executed.

## To install Ansible

- Install on control node( Master/Main controller)

- Must configure EPEL repository on system.

  ```bash
  sudo yum install ansible
  sudo yum install git # version control for playbooks
  ```

## Set up ansible

#### Configurations

```bash
vim /etc/ansible/ansible.cfg #primary Ansible config file
```

- Default inventory configuration 
- Default remote user

#### Inventory - Lists of servers/hosts

```bash
vim /etc/ansible/hosts - default inventory list of hosts that Ansible manages
```

1. Default : //ansible/hosts

2. Specify by CLI : ansible -i

3. Set in ansible.cfg


```bash
mail.example.com ansible_port=25 #set mail port

[webservers] 
webserverExample  ansible_host=httpd1.example.com #use alias as vars
[appservers]
appExample ansible_host=app1.example.com
[dbservers]
dbExample  ansible_host=db01.example.com
[labservers]bash
labExample ansible_host=lab01.example.com
```

## User Authentication

- Use ssh-keygen and ssh-copy-id
- Best implemented using a common user across all Ansible controlled system

```bash
ssh app1.example.com # connect to app1 ( member node)
sudo useradd ansible # add new user
sudo passwd ansible # set pw
```

#### **On Control Node**

```bash
exit # exit member node
ssh control-node.example.com 
sudo su - ansible # switch into super user Ansible
ssh-keygen # generate ssh key
ssh-copy-id app1.example.com # copy public key to member node
# will prompt for password
```

#### On Member Node

```bash
ssh root@app1.example.com
sudo visudo #allow users to execute commands as root

## Allow ansible(user) to run all command without password
## Security wise, can be more granular instead. Take note of permissions.
ansible ALL=(ALL) NOPASSWD: ALL 

## Can use -K instead for sudo pwd when connecting if no NOPASSWD privilege.
```

## [Ansible Docs](https://docs.ansible.com/)

```bash
man ansible-doc ##alternative methods
ansible-docs -l
```

## How it works?

**Playbook** contains **play**

Plays contain **tasks**

Tasks calls **modules**

Tasks run **sequentially**

**Handlers** are triggered by tasks, run once at the end of plays

## How to run

Ad-hoc: ansible <inventory> -m ,

Playbooks - ansible-playbook

Automation Framework: Ansible Tower

## Run with playbooks

Run a playbook on selected inventories from command line.

```bash
ansible-playbook <options>
ansible-playbook my-playbook.yml
```

## [Inventories](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)

- Static lists of servers
- Dynamic lists of servers: AWS, Azure,GCP.
- Two common type of formats: INI and YAML

**INI**

```ini
##etc/ansible/hosts
mail.example.com

[webservers]
foo.example.com
bar.example.com

[dbservers]
one.example.com
two.example.com
three.example.com

```

##### **YAML**

```yaml
all:
  hosts:
    mail.example.com:
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

## [Ad-hoc Ansible Commands](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html)

- Comparable to bash commands

```bash
#Syntax
ansible <host> -b -m <module> -a "<arg1 arg2>" 
## Host is a host or host group defined in Ansible inventory file
-b ##escalate permission to become superuser(root) default sudo.
-m ##module and allow commands to be used
-a #allow params to pass. If used without modules, run shell commands on target system.

#Example
ansible appExample -m yum -a "name=httpd state=latest"
ansible appExample -m copy -a "src=/etc/hosts dest=/tmp/hosts"
ansible webservers -m service -a "name=httpd state=started"
##States- absent,installed,latest,present,removed

```

##### [States for yum module](https://docs.ansible.com/ansible/latest/modules/yum_module.html)

- To install (`present` or `installed`, `latest`)

- To remove (`absent` or `removed`) a package.

- `present` and `installed`  ensure that a desired package is installed.

- `latest` will update package if it is not latest.

- `absent` and `removed` will remove the package

Default is `None` or `Present`

## [Playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html)

- YAML - describe desired state of something

- Can be used to build entire application environment
- Comparable to bash script
- Indent matters!
- Can contain multiple plays

```yaml
---
- hosts: webservers
  become: yes
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
  - name: write the apache config file
    template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf
    notify:
    - restart apache
  - name: ensure apache is running
    service:
      name: httpd
      state: started
  handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
```

## Variables

- Names should be alphabet,numbers and underscores, always start with a letter.

- Can be scoped by group,host or within playbook.

- Reference using double curly braces

```ini
hosts: "{{ my_host_var }}"
```

```bash
CLI Example
ansible-playbook service.yml -e \
"target_hosts=localhost \
target_service=httpd";
```

[Playbook Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html) 

```yaml
Playbook Example
---
hosts: webservers
become: yes
vars: 
	target_service: httpd
	target_state: started
tasks:
  - name: Ensure target state
    service:
	 name: "{{ target_service }}"
	 state: "{{ target_state }}"
```

## Ansible [Facts](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variables-discovered-from-systems-facts)

- Variables that are discovered not set by user.

- Derived from speaking with remote system

- Properties regarding a given remote system

  ```bash
  ansible hostname -m setup ##return all facts
  ansible appServer -m setup -a filter=*hostname* #to find hostname use Regex
  ```

- Can be referenced in playbooks

  ```bash
  {{ ansible_facts['default_ipv4']['address'] }} ##get ip
  ```

#### **Can be disabled**

```yaml
##webserver yaml
- hosts: webservers
  become:yes
  gather_facts: no
```

## Handlers

- Mechanism that allows an action to be flagged for execution when a task performs a change
- **Notify** and **Listen** keyword
- **Notify** will **only flag handler if a task block make changes.**
- **No matter how many times it is flagged, only runs during a play's final phase**

```yaml
-name : template config file
 template:
  src: foo.conf
  dest: /etc/foo.conf
 notify:
  - restart memcached
  
- name : restart memcached
  service:
   name: memcached
   state: restarted
  listen: "restart memcached"
```

## Troubleshooting and Debug

- **Debug** module and **Register** module
  - Print information about in-progress plays
- Debug take 2 primary parameters that are mutually exclusive
  - msg: A message printed to STDOUT
  - var: A var whose content is printed to STDOUT

```yaml
# Register with Debug Example
- hosts: webservers
  tasks:
  - name: add web content
  	lineinfile:
  		line: "{{ ansible_hostname }}"
  		path: /var/www/html/index.html
  	register: task_debug
  - debug:
      msg: "Output of lineinfile is {{ task_debug }}" # < msg and var
      

# Debug Example
- debug:
	msg: "System {{ inventory_hostname }} has uuid {{ansible_product_uuid}} "

```

### [Check mode / Dry run](https://docs.ansible.com/ansible/latest/user_guide/playbooks_checkmode.html)

- Dry-run for ad-hoc commands and Playbooks
- Validate playbook runs before making state changes on target systems

```bash
#Bash example
ansible-playbook foo.yml --check
```

```yaml
  # Playbook example
  - name: this task will make changes to the system even in check mode
    command: /something/to/run --even-in-check-mode
    check_mode: no

  - name: this task will always run under checkmode and not change the system
    lineinfile:
        line: "important config"
        dest: /path/to/myconfig.conf
        state: present
    check_mode: yes
```

