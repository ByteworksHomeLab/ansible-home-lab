# Ansible Home Lab

This repository contains Ansible scripts for my Kubernetes home lab. I made it public to support my blog: [smitchell.github.io](https://smitchell.github.io).

# Description

*I modeled this project after [Jakub Skałecki's](https://rock-it.pl/author/jakub/) project in GitHub: [github.com/Valian/ansible-multienv-base](https://github.com/Valian/ansible-multienv-base).*

# Structure
```
|- hosts
|  |- shared-secrets.yml  # encrypted vars, used in all environments
|  |- shared-vars.yml     # not encrypted vars, used in all environments
|  |- prod                # directory for prod environment
|  |  |- inventory        # inventory file with definitions of all required hosts
|  |  |- secrets.yml      # encrpted vars for this environment
|  |  |- groups_vars
|  |     |- all.yml       # normal group_vars, like in typical ansible project
|  |- test                # directory for test environment
|     |- inventory
|     |- secrets.yml
|     |- groups_vars
|        |- all.yml
|- roles                  # all ansible roles used in playbooks
|  |- role1
|     |- ...
|- playbook1.yml          # here we put our playbooks
```

# Usage

To do something similar, clone [Jakub Skałecki's](https://rock-it.pl/author/jakub/) repository.
```
git clone https://github.com/Valian/ansible-multienv-base
```
Next, create playbooks, fill inventories and variables, create roles etc. 
To encrypt secret variables file, use
```
ansible-vault encrypt path-to-file     
# Caution! The password must be the same in every encrypted file
```

See /Users/stevemitchell/.ansible.cfg for the default values. It allows us to leave off most of the parameters.
Examples of ad-hoc Ansible command with the default values

```shell
ansible all --list-hosts.yml
ansible all -m ping
ansible all -m gather_facts
ansible all -m apt -a update_cache=true --become --ask-become-pass
ansible all -m apt -a name=vim-nox --become --ask-become-pass
ansible all -m apt -a name=hardinfo --become --ask-become-pass
ansible all -m reboot --become --ask-become-pass
```

There is also a special task, that we should include in pre_tasks in each playbook, called tasks/load_vars.yml.

To get an ansible playbook.
```shell
ansible-galaxy collection install community.postgresql
```

To run playbook against the specified environment, we must use this command format:
```
ansible-playbook  --ask-become-pass setup_new_hosts.yml
ansible-playbook --ask-become-pass apt_update.yml 
ansible-playbook --ask-become-pass --limit dbservers apt_update.yml 
ansible-playbook --ask-become-pass --limit maascserver apt_update.yml
cd 
ansible-playbook --ask-become-pass playbooks/postgresql/postgres_install.yml


