# Ansible

- Automation engine that allows for agentless system configuration and deployments

- Operates over SSH and runs Ansible modules on remote system in order to complete task

- Installed on a single control node where a list of host inventory filesaand playbooks are kept
- Heavy lifting performed on remote host, where Ansible Modules are executed.



## Playbooks

YAML - describe desired state of something

Can be used to build entire application environment

### Variables 

- Playbooks
- Files
- Inventories(group vars,host vars)
- Command line
- Discovered variables(facts)
- ansible tower

### Inventories

- Static lines of servers

- Ranges

- Custom things

- Dynamic lists of servers: AWS, Azure,GCP.


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



## Check mode / Dry run

- Dry-run for ad-hoc commands and Playbooks
- Validate playbook runs before making state changes on target systems







# To install Ansible

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

#### Inventory

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

## Finding Docs

[Ansible Docs](https://docs.ansible.com/)

or 

```bash
man ansible-doc
ansible-docs -l
```



## Ad-hoc Ansible Commands

```bash
#Syntax
ansible <host> -b -m <module> -a "<arg1 arg2>" 
-b ##become sudo
-m ##module
-a #allow params to pass. If used without modules, run shell commands on target system.

#Example
ansible appExample -m yum -a "name=httpd state=latest"

##States- absent,installed,latest,present,removed
```

##### [States for yum module](https://docs.ansible.com/ansible/latest/modules/yum_module.html)

- To install (`present` or `installed`, `latest`)

- To remove (`absent` or `removed`) a package.

- `present` and `installed`  ensure that a desired package is installed.

- `latest` will update package if it is not latest.

- `absent` and `removed` will remove the package

Default is `None` or `Present`