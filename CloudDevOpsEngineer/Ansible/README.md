# Ansible

- System configuration,  software deployment, and infrastructure orchestration for continuous deployment.
- Uses YAML to write Ansible playbooks that describe automation tasks
- Agent-less that uses SSH to connect to servers
- Written in Python, Ruby and Powershell

- Light-weight but slow in managing large clusters.

## What are Playbooks?

Playbooks are the YAML files that describe the policy for configuration, deployment, and orchestration tasks. These playbooks are utilized by the "Ansible Modules" to manage configurations and deployments to remote machines (nodes). Playbooks can deploy multi-tier rolling updates and can assign actions for other nodes.



#### Ansible Templates

- Ansible templates enable you to apply configuration files which are customized to fit the application
- Variables are abstracted using the format: `{{ VARIABLE_NAME }}`



### Templates Example Code

File: repo.conf.j2

```
server {
        listen   80;
        server_name  {{ SERVER_NAME }};
        root   {{ REPO_PATH }};
        location / {
                index  index.php index.html index.htm;
                autoindex on;    #enable listing of directory index
        }
}
```



### Apply Template

```
- name: generate all configmap configs
  template:
      src: repo.conf.j2
      dest: /etc/nginx/conf.d/repo.conf
      owner: root
      group: root
      mode: 0744
```

### Conditionals Register

Set an Ansible variable to the output of the module

```
- name: How long has the server been up
  shell: uptime
  register: sys_uptime
```

#### Built-in Ansible Variables

- To see every built-in variable for the system you are using (every host is different) (change localhost to the host you are checking): `$ ansible -m setup localhost`
- Some common ones:
  - ansible_hostname
  - ansible_all_ipv4_addresses
  - ansible_architecture
  - ansible_kernel

### File Structure of a Role

Each role is contained in a subdirectory: `roles/name_of_role`

Containing additional directories:

- Tasks
- Templates
- Vars
- Files



To include a role:

```
    - name: Initialize Vault Server
      include_role:
        name: vault
```



### Reading Resources

- [Ansible templates](https://docs.ansible.com/ansible/latest/modules/template_module.html)
- [Ansible systemd](https://docs.ansible.com/ansible/latest/modules/systemd_module.html)
- [Ansible conditionals](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html)
- [Ansible package installs](https://docs.ansible.com/ansible/2.5/modules/package_module.html)
- [Ansible inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
- [Ansible roles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html)