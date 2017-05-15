# Ansible

Worth to take a look: [Ansible Documentation](http://docs.ansible.com/).


## Digris Roles

Commonly used (and potentially reusable) [roles](http://docs.ansible.com/ansible/playbooks_roles.html) are handled on 
Digris' [GitHub Account](https://github.com/digris).


## Configuration


### (Local) Ansible Setup & Configuration

Ansible version >=2.2 should be installed - recommended way to install is via `pip`:

    pip install ansible
    
Note: "global" installation is fine and no `virtualenv` is necessary. 


The default location of the ansible (user) 
[configuration](http://docs.ansible.com/ansible/intro_configuration.html#configuration-file) is `~/.ansible.cfg`

Create/check the config file, it should contain (at least) the `roles_path`:
 
    [defaults]
    # roles directory
    roles_path = ~/ansible/roles
    # optionaly disable ssh host key checking
    host_key_checking = False



## The Digris Infrastructure / NOC Repository
 
The [infrastructure repository](https://noc.digris.ch/projects/infrastructure/repository) is - for some reasons - 
not a public GitHub project :)


### Repository

Add your ssh public key under [noc.digris.ch/my/public_keys](https://noc.digris.ch/my/public_keys) and clone the
repository:

    cit clone ssh://git@git.digris.ch/infrastructure.git ~/code/digris/infrastructure


### Requirements (Required Roles)

The *infrastructure repository* acts as a "controller" and does only include higher-level playbooks. The actually required roles 
(incl. respective versions) are referenced in the
[`ansible/requirements.yml`](https://noc.digris.ch/projects/infrastructure/repository/revisions/master/entry/ansible/requirements.yml) 
file.

To install or update the requirements run:

    ansible-galaxy install -r requirements.yml --force
    

This will install/update the roles into the `roles_path` defined in your `~/.ansible.cfg`.


### Inventory

The ["inventory"](http://docs.ansible.com/ansible/intro_inventory.html#inventory) is defined in the
[`ansible/hosts`](https://noc.digris.ch/projects/infrastructure/repository/revisions/master/entry/ansible/hosts) 
file.

Note: This is just a minimalistic first version :) - but the idea is to have the complete *inventory* in one single
file. (or in a script/tool that exposes the inventory to `stdout`...)

Limitations to a subset of the inventory should be handled on a playbook (or playbook execution) level.



## Playbook & Role Conventions

Note: This is an open topic & everyone is invited to propose better ways :)

We have to make sure that the infrastructure can be tested & deployed in a reproducible way. This requires that 
roles & settings can be handled by VCS. On the other hand, working/developing bits should be as straight-forward as
possible.


### 'Prod' & 'Dev' Playbooks & Roles
 
When looking at the 
[infrastructure playboks](https://noc.digris.ch/projects/infrastructure/repository/revisions/master/entry/ansible/) 
the playbooks are organized in the form of `<playbook_name>.yml` and `<playbook_name>-dev.yml`.

The latter playbook is intended for development & testing - the containing *roles* should match (structurally) the ones 
from the "non-dev" version, but likely will point to the "dev" version of respective roles.


