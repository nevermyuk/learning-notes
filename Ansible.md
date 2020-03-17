# Ansible

- Simple tool for doing 'remote things' . Allows to define and run a single task ' playbook' against a set of hosts.
- Automation engine that allows for agentless system configuration and deployments
- Operates over SSH and runs Ansible modules on remote system in order to complete task
- Installed on a single control node where a list of host inventory files and playbooks are kept
- Heavy lifting performed on remote host, where Ansible Modules are executed.

## Basics of Ansible

- Consist of 
  - A control node that send **Instructions.**
  - One or more member nodes (group of hosts) to receive instructions.

## How to send Instructions?

1. Ad-hoc commands

   ```bash
   ansible <host> -b -m <module> -a "<arg1 arg2>"
   ```

2. Playbooks - ansible-playbook

   ```
   ansible-playbook <options>
   ansible-playbook my-playbook.yml
   ```

3. Automation Framework: Ansible Tower

## How it works?

**Inventory** are list of hosts/servers grouped or ungrouped.

**Ansible** runs **Ad-hoc command** or **Playbooks** on **Inventory**.

**Playbook** contains **Plays**

**Plays** contain **tasks**

**Tasks** calls **modules**

**Tasks** run **sequentially**

**Handlers** are **triggered** by **tasks**, **run once** **at** the **end of plays**

## Steps to get Ansible Up and Running

1. Install Ansible using package manager

   - Install Git - Advisable

     ```bash
     ssh user@control-node.com
     sudo yum install ansible
     sudo yum install git # source control for playbooks
     ```

2. Set up **Common users with SSH keys** for **each host** that is to be controlled with Ansible.

   - **Control nodes and all member nodes**

     ```bash
     useradd ansible
     passwd ansible
     ```

   - Setup SSH access and passwordless login between Ansible controlled systems for ease of automation.

     - On **Control node**, generate ssh key and copy key to member nodes.
       ```bash
       ssh-keygen
       ssh-copy-id ansible@<member-node address> #for all member nodes
       # Allows secure ssh connection from control nodes into member nodes.
       ```

     - On **All member node** edit '/etc/sudoer' . - Escalate permissions without password

       ```bash
       sudo visudo
       # etc/sudoers
       ansible ALL=(ALL) NOPASSWD:ALL #add in
       # Allow ansible user to use sudo command without any password for automated systems.
       ```

3. Set up Ansible.cfg Configuration for **Control Node**

   ```bash
   sudo vim /etc/ansible/ansible.cfg
   ```

4. Set up **Inventory** (list of hosts Ansible manages)

   ```bash
   sudo vim /etc/ansible/hosts
   ```

5. **Ansible is all set!**

6. Use **Ad-hoc commands** or **Playbooks** to get started!

   - [Examples](##Examples)

## How To Install Ansible

- Install on control node( Master/Main controller)

- Must configure EPEL repository on system.

```bash
ssh user@control-node.com
sudo yum install ansible
sudo yum install git # source control for playbooks
```

## Common User Authentication

- Use ssh-keygen and ssh-copy-id
- Best implemented using a common user across all Ansible controlled system

#### **On Control Node**

```bash
ssh user@control-node.com 
sudo useradd ansible
```

#### On Member Node

```bash
ssh user@app1.example.com # connect to app( member node)
sudo useradd ansible # add new user
sudo passwd ansible # set pw
```

#### Return to Control Node

```bash
logout
sudo su - ansible # switch into super user Ansible
ssh-keygen # generate ssh key
ssh-copy-id app1.example.com # copy public key to member node
							 # will prompt for password
ssh app1.example.com #able to login without password because of correct ssh setup!
```

#### Return to Member Node

```bash
ssh root-user@app1.example.com # will ask for passwd
sudo visudo #allow users to execute commands as root


ansible ALL=(ALL) NOPASSWD: ALL  ## Add into visudo config.
## Allow ansible(user) to run all command without password
## Security wise, can be more granular instead. Take note of permissions.

## Can use -K instead for sudo pwd when connecting if no NOPASSWD privilege.

```



## Set up Ansible Configuration

#### Return to Control Node

```bash
logout
vim /etc/ansible/ansible.cfg #primary Ansible config file
#alternative
ansible-config list
```

- Set Default Inventory configuration 
**Config properties may be overridden using local files and searched in the following order.**
  1. ANSIBLE_CONFIG(environment variable if set)
  2. ansible.cfg(in current directory)
  3. ~/.ansible.cfg(in home directory)
  4. /etc/ansible/ansible.cfg

## Set up Inventory

#### On Control Node

Set up hosts configuration

```bash
vim /etc/ansible/hosts - default inventory list of hosts that Ansible manages
```

1. Default : /etc/ansible/hosts

2. Specify by CLI : 

```bash
ansible -i <inventory-filename> localhost -m ping
```

3. **Can be set in ansible.cfg**

```ini
# Example inventory 
#/inventory
mail.example.com ansible_port=25 #set mail port

[webservers] 
webserverExample  ansible_host=httpd1.example.com #use alias as vars
[appservers]
appExample ansible_host=app1.example.com
[dbservers]
dbExample  ansible_host=db01.example.com
[labservers]
labExample ansible_host=lab01.example.com
```

## [Ansible Docs](https://docs.ansible.com/)

```bash
man ansible-doc ##alternative methods
ansible-docs -l
```

## [Inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)

- Can be **static or dynamic**
- **Tip** : Best practice to have one for production and one for staging.

### Static Inventory

- Two common Formats : INI and YAML

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



### Dynamic Inventory

- Can be in any compiled language such bash/python as long as it outputs **JSON**
- Generally provided by cloud providers



## [Ansible Modules](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html)

- May take arguments
- Most take key=value argument, delimited by whitespace

#### Common Modules

- Ping - Verify connectivity
- setup - Collect Ansible Facts
- yum - Package manager
- service - Control service daemons
- copy - copy file from location to target host.

#### Command,shell and script modules

- Command module parameters
  - create : create file
  - removes : removes file
  - chdir : change directory

  ```bash
  ansible localhost -a "touch /filetoRemove creates=/filetoRemove"
  ansible localhost -a "rm -f /filetoRemove removers=/filetoRemove"
  ```

  To use with ansible ad-hoc when no module specified

- Use command module unless require shell features.
  
- Such as output redirection
  
- Shell module

  ```bash
  ansible localhost -m shell -a "echo $PATH > /tmp/file"
  ```

- Script module to run full shell scripts.

#### File and Copy module

- File 

  - Create,delete and modify file properties

  ```bash
  ansible -i inventory localhost -m file -a "name=file state=touch" #create file
  ansible -i inventory localhost -m file -a "name=file state=absent" #remove-file
  ```

- Copy to copy files between systems.

  ```bash
  ansible -i inventory workstations -m copy -a "src=testfile dest=onRemoteFile"
  ```

#### Lineinfile module

- Insert text into a given file

  ```bash
  ansible -i inventory workstation -m lineinfile -a "path=/home/ansible/testfile line='Hello Ansible' state=present"
  ```

#### Get_url module

- Download file from HTTP/HTTPS/FTP hosts with authentication and verification

- Need absolute destination

  ```bash
  ansible localhost -m get_url -a "url=http://google.com dest=/home/ansible/test.html"
  ```

#### Archive and unarchive module

- Compress and unzip files

  ```bash
  ansible -i inventory workstation -m archive -a "path=/home/ansible format=tar dest=/tmp/testfile.zip"
  ```

#### User module

- Create user,remove user and change user properties.

- Take note to append if not will be overwritten.

  ```bash
  ansible -i inventory workstation -b -m user -a "name=super"
  ```

  ```bash
  ansible -i inventory workstation -b -m user -a "name=super state=absent remove=yes"
  #make sure to add remove flag so artifacts are removed
  ```

#### Group module

- Create groups and change group properties

  ```bash
  ansible -i inventory workstation -b -m group -a "name=testGroup"
  ansible -i inventory workstation -b -m group -a "name=super group=testGroup"
  #add user to group
  ```

- Can only delete group if no user in group.

  ```bash
  ansible -i inv remote -b -m group -a "name=testGroup state=absent"
  ```

#### Package manager modules

- package,yum,apt.
- package will automatically detect target host distro and use appropriate method of installing software
- Best practice, use package for mixed distro environment.

  ```bash
  ansible all -b -m package -a "name=httpd state=latest"
  ```

**Tips ** : Module names are different in different distros. Take note of this difference.

#### Service module

- Control system daemons
- Compatible with systemd and older BSD init style of daemon management

# To start service
```bash
ansible -i inventory workstation -b -m service -a "name=httpd state=started enabled=yes"
```

  # To get service status
```bash
ansible -i inventory workstation -b -m service -a "name=httpd"
```




## [Ad-hoc Ansible Commands](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html)

- Comparable to bash commands

```bash
#Syntax
ansible <host> -b -m <module> -a "<arg1 arg2>" -f <Number of forks>
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

### Asynchronous Commands

- When operations require time to execute

- Options / Flag to use

  - **-B** to provide a timeout value and initiate operation.

    ```bash
    ansible localhost -a "/home/ansible/sleep.sh" -B 10
    ```

  - **-P** to engage polling of operation on a set interval - check status of operation at set intervals!

    ```bash
    ansible localhost -a "/home/ansible/sleep.sh" -B 10 -P 0
    ```

- **Async_status module** - can be used to check status too.

## [Playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html)

- Use YAML - to describe desired state.
-  Best practice: YAML files to open with 3 hyphens and end with 3 hyphens
- Can be used to build entire application environment
- Comparable to bash script
- **Indent matters! Must be consistent!**
- Can contain multiple plays that can have multiple tasks 

```yaml
YAML Specifics for Playbook
---
# List - Members to start with hyphen and same indent level.

Users:
- Chef
- Joe
- Ravin

# Dictionary - key value pair ; Designated with colon and a space

User:
  name: Servir
  job: Consultant
  salary: 143000
  
# Two characters to indicate Multi-line value 
# Pipe | and  Greater than sign > 
## Pipe will include newlines ##

ports: |
  80
  256
  443
  3000
# Interpret as is
## Greater than > will ignore/fold newline ##

ports: >
 80
 256
 443
 3000
# Interpret as: 80 256 443 3000

# Special characters : [] , {} : > | 
## If meant to be literals, should be wrapped in double quotes.

# Ansible Variables indicated with curly braces, {{ variable }}.
# To be wrapped with double quotes to prevent being interpreted as dictionary.

"{{ var_names }}"

# Booleans automatically converted in Ansible.

become: yes

# Floating Point numbers are taken as numbers by default.
## Wrap with double quote to escape 

version: "1.0"

```

## Tasks

- Tasks are the use of Ansible modules within a play.
- Syntax
  - List beginning with name property that describes with a statement of what the tasks does.
    - **displays on task execution and serve as basic documentation within playbook**
  - Each list item will start with a hyphen -.

```yaml
---
- hosts: localhost
  become: yes
  tasks:
# Setting up webserver
   - name: install httpd
  	 yum:
      name: httpd
      state: latest
```

### Example Playbook

```yaml
---
- hosts: webservers
  become: yes
  tasks:
   - name: install software
     yum:
       name: httpd
       state: latest
   - name: install additional software
     yum:
       name: elinks
       state: latest
```

### Playbook Command

- Execute Playbooks using **ansible-playbook**.
  - Arguments
    - Playbook to execute
  - Flags
    - -i -- to select inventory file to use
    - -K(capital) -- ask for **become** password
    - -k(lowercase) -- ask for **connect** password
    - -C(capital) -- run in check mode ; dry run of provided playbook.



## Variables

- Use to customize config values and hold return values.
- Names should be alphabet,numbers and underscores, always start with a letter.
- Can be scoped by group,host or within playbook.
- Can store 
  - Text or number
  - simple list
  - python style dictionary
- Defined in 
  - CLI
  - Variable file
  - Playbook
  - Inventory

- **Reference using double curly braces and wrap in double quotes.**

```yaml
hosts: "{{ my_host_var }}"
# Dictionary , call the key.
hosts: "{{ my_host_var['firstHost'] }}"
```

```bash
CLI Example
ansible-playbook service.yml -e \
"target_hosts=localhost \
target_service=httpd";
```

[Playbook Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html) 

```yaml
--- # Playbook example
- hosts: webservers
  become: yes
  vars: 
    target_service: httpd
    target_state: started
    register: cmd_output # Register variable
   tasks:
     - name: Ensure target state
     service:
       name: "{{ target_service }}"
       state: "{{ target_state }}"

```

[**Register variable**](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#registering-variables) -- save results of commands as a variable.

- To extract use copy module.

```yaml
tasks:
- name: write command module output to file
  copy:
    content: "{{ cmd_output }}"
    dest: /cmd_output.txt
```

#### Variable File

- To save variables in a file 

- 2 methods to include in playbook


1. By CLI


```bash
ansible-playbook playbook.yml -e @vars.yml
```

**-e -- flag treat succeeding arguments as variable file.**

2. By including vars_files in playbook.


  ```yaml
# file: /home/ansible/example_vars.yml
target_service: httpd
target_state: started
  ```

  ```yaml
---
- hosts: webservers 
  become: yes 
  vars_files: /home/ansible/example_vars.yml 
  tasks: 
  - name: Ensure target service at target state 
    service: 
      name: "{{ target_service }}" 
      state: "{{ target_state }}"
  ```



## Templates with Template module

- Files with Ansible Variable inside that are substituted on play execution
- Use template module
- Text files with variable reference
- Jinga 2 templating : example.j2
- Use case : skeleton config file where variables are dynamic and customizable.

```jinja2
#config.j2
name={{ code_name }}
version={{ version }}
```

```yaml
---
- hosts: localhost
  become: yes
  vars:
   code_name: woofWoof
   version: 3.1
  tasks: 
   - name: deploy config file # task will substitute variable into config file.
     template:
       src: config.j2
       dest: /opt/config 
```

**Tip : -validate flag to help validation of configuration file**

## Ansible [Facts](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variables-discovered-from-systems-facts)

- Properties regarding a given remote system

- **Use setup module to retrieve facts**.

  - Parameters 
    - "filter=RegExp" to prune fact output.
    - --tree option to save all facts of remote systems.

- Variables that are discovered not set by user.

- Derived from speaking with remote system

  ```bash
  ansible hostname -m setup ##return all facts
  
  ansible appServer -m setup -a "filter=*dist* #to find hostname use Regex
  ```


- Can be referenced in playbooks using brackets or dot notation

  ```bash
  "{{ hostvars['localhost']['ansible_default_ipv4']['address'] }}"
  "{{ hostsvars.stat.exists }}"
  ```

- **--tree**

  - JSON output of facts

- **Can be Disabled with gather_fact keyword: no**

```yaml
##webserver yaml
- hosts: webservers
  become:yes
  gather_facts: no
```

### Fact caching

- Set to memory by default
  - jsonfile by default
  - **Use Redis** if required
- Configure in ansible.cfg 

### Conditional Execution in Playbook

- Making plays conditional

- **when** keyword used to test condition within a playbook.

- Can use Jinja2 expression for conditional evaluation.

- Using facts

  ```yaml
  --- #facts example
  tasks: 
  - name: "shut down Debian systems" 
    command: /sbin/shutdown -t now when: ansible_os_family == "Debian" 
  # note that Ansible facts and vars like ansible_os_family can be used 
  # directly in conditionals without double curly braces 
  ```

- Module output conditionally

  ```bash
  --- #conditional example
  - hosts: web_servers 
    tasks: 
    - shell: /usr/bin/example
      register: example_output
      ignore_errors: True
     
    - shell: /usr/bin/bar when: example_output.something == 123
  ```

  

### Loops in playbooks

- **loop** keyword
- Can operate with a list variable
- Can combine with conditional

```yaml
--- #loop example
- hosts: localhost
  become: yes
  tasks:
    -name: install software
     yum:
       name: "{{ item }}" #loop each item
       state: latest
      loop: 
        - httpd
        - nmap-ncat
        - elinks
```

```yaml
--- #loop example with var files
- hosts: localhost
  become: yes
  vars_files:
   - vars.yml
  tasks:
    -name: install software
     yum:
       name: "{{ item }}" #loop each item
       state: latest
      loop: "{{ software_list }}"
```

```yaml
#vars.yml
software_list:
- httpd
- nmap-ncat
- elinks
```



## Handlers

- Mechanism that allows an action to be flagged for execution when a task performs a change.
  - More efficient
- **Notify** and **Listen** keyword
  - names are to be exact for **notify**
  - **listen** can be generic topics -- easier to trigger multiple handlers
- **Notify** will **only flag handler if a task block make changes.**
- **No matter how many times it is flagged, only runs during a play's final phase**

```yaml
- name : template config file
 template:
  src: config.j2
  dest: /etc/config.conf
 notify:
  - restart memcached
  - restart cache service
handlers: 
- name : restart memcached
  service:
   name: restart memcached
   state: restarted
  listen: "restart cache service "
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

## [Check mode / Dry run](https://docs.ansible.com/ansible/latest/user_guide/playbooks_checkmode.html)

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

## Parallelism

- Ansible uses **Forks** to execute tasks in parallel

- Each **fork** is able to run a tasks against a target host

- Default is **5 Forks** can be changed, 

  - In ansible.cfg
  - With -F or --forks flag

- **The more Forks, the more system resource will be required on Ansible control node** 

  

### How Forks work?

- Assuming one run a module against 5 hosts, module will execute simultaneously on all host.
- **If more than 5 hosts are targeted,** hosts after the first 5 must wait for a fork to complete before forks will execute on the waiting hosts.



## Examples 

### Ad-Hoc Commands

#### Control Node

```bash
ansible -i inventory workstations -b -m yum -a "name=elinks state=installed"
```

### Playbook

#### Control Node

```bash
pwd #/home/ansible
vim inventory  # add hostname to inventory file , e.g. workstation
#inventory
[workstation]
app1.example.com
```

```yaml
#Playbook example
vim git-setup.yml
#git-setup.yml
--- # install git on target host
- hosts: workstation
  become: yes
  tasks:
  - name: install git
    yum:
     name: git
     state: latest
```

```bash
ansible-playbook -i inventory git-setup.yml #install with playbook on inventory(workstation)
```

