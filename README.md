# Ansible Home Lab

This repository contains Ansible scripts for my Kubernetes home lab. I made it public to support my blog: [smitchell.github.io](https://smitchell.github.io).

# Description

*This project is modeled after [github.com/Valian/ansible-multienv-base](https://github.com/Valian/ansible-multienv-base.*

# Structure
```
|- hosts
|  |- shared-secrets.yml  # encrypted vars, used in all environments
|  |- shared-vars.yml     # not encrypted vars, used in all environments
|  |- environment1        # directory for environment (e.g. test or staging)
|  |  |- inventory        # inventory file with definitions of all required hosts
|  |  |- secrets.yml      # encrpted vars for this environment
|  |  |- groups_vars
|  |     |- all.yml       # normal group_vars, like in typical ansible project
|  |- environment2        # directory for other environment
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
ansible-vault encrypt path-to-file     # Caution! The password must be the same in every encrypted file
```

There is also a special task, that we should include in pre_tasks in each playbook, called tasks/load_vars.yml.
To run playbook against the specified environment, we must use this command format:
```
ansible-playbook -i hosts/desired_env/inventory --ask-vault-pass playbook.yml
