ansible_molecule_template
=========


[![CI](https://github.com/habbis/ansible_molecule_template/workflows/CI/badge.svg)](https://github.com/habbis/ansible_molecule_template/actions?query=workflow%3ACI)


My template role for ansible.

This role have github-action molecule to test playbook.

I am also using stabel issue bot with this repo.

Its under here

```
 .github/stale.yml

```

see Jeff Geerling blog post about it.

[Enabling a stale issue bot on my GitHub repositories](https://www.jeffgeerling.com/blog/2020/enabling-stale-issue-bot-on-my-github-repositories)

Github-action molecule is under.

```
.github/workflows/ci.yml

```
In the github action file ci.yml you
can set diffrent linux distros under test matrix.

There is also setup yamllint with github-action.

Here you can also exclude files.

```
---
extends: default

rules:
  line-length:
    max: 200
    level: warning

ignore: |
  .github/stale.yml
  .travis.yml
  site.yml

```

There is also .ansible-lint file for linting ansible.

To install molecule locally with docker driver.

```
pip3  install --user molecule[docker]

```

Molecule settings are under molecule/default/
there you have the files converge.yml, molecule.yml .

converge.yml here you call the role so molecule can run it 
just like a site.yml file.

```
- name: Converge
  hosts: all
  become: true
  gather_facts: yes
  # import a variable file
  #vars_files:
  #  - ../../defaults/main.yml
  # variables
  #vars:


  tasks:
    - name: "Include ansible_molecule_template"
      include_role:
        name: "ansible_molecule_template"
```

molecule.yml is the central configuration file for the testing and
here you can choose what docker image it will use for testing.

```
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: instance
    # image is for jeff gerling
    image: "geerlingguy/docker-${MOLECULE_DISTRO:-centos7}-ansible:latest"
    command: ${MOLECULE_DOCKER_COMMAND:-""}
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    privileged: true
    pre_build_image: true
provisioner:
  name: ansible
  playbooks:
    converge: ${MOLECULE_PLAYBOOK:-converge.yml}
scenario:
  # comment out what you dont need for testing
  test_sequence:
    - lint
    - destroy
    - dependency
    - syntax
    - create
    - prepare
    - converge
    - idempotence
    - check
    - side_effect
    - verify
    - destroy
```

To test on your machine with molecule since the driver is docker 
that need to be installed.

Run the molecule test.

```
molecule test
```

To just run the playbook to see if you have any problems.

```
molecule converge
```

See [molecule doc](https://molecule.readthedocs.io/en/latest/getting-started.html) for more info.


To change role name in all files in the folder.

```
find . -type f -print0 | xargs -0 sed -i "s/ansible_molecule_template/new_role/g"

```

And for macos since apple is special.

```
find . -type f -print0 | LC_ALL=C  xargs -0  sed -i "" 's/setup_puppet_agent/ansible_role_puppet_agent/g'
```

Example site.yml

```
---
- name: setup puppet agent
  gather_facts: yes
  remote_user: root
  #remote_user: ansible
  #become: yes
  #become_method: sudo
  hosts: test
  #hosts: puppet-clients
  #hosts: all
  vars_files:
    -  defaults/main.yml
    #-  defaults/secrets.yml

  roles:
    - { role: ../ansible_molecule_template }
```
