# Test Driven Infrastructure with Ansible

## Part 3: Variables, Conditionals, Loops

Playbooks can be made more dynamic or configurable by using variables. Variables in Ansible can be defined in multiple
ways and are injected into playbooks using [Jinja2
templating](https://docs.ansible.com/ansible/playbooks_variables.html#using-variables-about-jinja2). Within a Jinja
template, you have access to all variables that are in scope for that host (facts are available as variables too). 

Variables can be defined on a playbook level by specifying the `vars` key for a play.

```YAML
---
- hosts: all
  become: yes
  vars:
    database_name: codedinbraam
    database_user: dev
    database_password: dev.password
  tasks:
    - name: install mysqldb
      apt: name=mysqldb-server state=present update_cache=true

    - name: install ansible module dependencies
      apt: name=python-mysqldb state=present

    - name: create database `codedinbraam`
      mysql_db: name={{ database_name }} state=present

    - name: create user
      mysql_user:
        name: {{ database_user }}
        password: {{ database_password }}
        priv: 'codedinbraam.*:ALL'
        state: present
```

### Loops

You will notice there is some duplication in that the first two tasks both use the `apt` module to install mysqldb and
ansible dependencies. This may seem fine for two packages but what if we needed to install dozens of packages? It's a
bit too redundant to write the same task step for each package, so we can use
[loops](https://docs.ansible.com/ansible/playbooks_loops.html) instead. Ansible's loops look very
different to those in programming languages such as Java or Python, but in principle they're the same.

```YAML
- name: install mysqldb
  apt: name=mysqldb-server state=present update_cache=true

- name: install ansible module dependencies
  apt: name=python-mysqldb state=present
```

Can be refactored to become:

```YAML
- name: install mysqldb and ansible dependencies
  apt: name={{ item }} state=present update_cache=true
  with_items:
    - mysqldb-server
    - python-mysqldb
```

### Conditionals

Logical branching can also be achieved in Ansible by using the `when` statement. We can for example, choose to install
MariaDB instead of MySQL based on a logical test. Using this, we can conditionally include appropriate tasks.

```YAML
---
- hosts: all
  become: yes
  vars:
    database_name: codedinbraam
    database_user: dev
    database_password: dev.password
  tasks:
    - include: tasks/mysqldb.yml
      when: 
        - ansible_distribution == "Ubuntu"
        - ansible_distribution == "CentOS"

    - include: tasks/mariadb.yml
      when: ansible_distribution == "CentOS"
```

In the example above, we decomposed our tasks into two separate YAML files, `mysqldb.yml` and `mariadb.yml`. Which set
of tasks gets applied will depend on the `ansible_distribution` fact of the host this playbook runs against.

You can also load variables conditionally using templates and the `vars_files` directive.

```YAML
---
- hosts: all
  become: yes
  vars_files:
    - {{ 'vars/ubuntu_vars.yml' if ansible_distribution == 'Ubuntu' }}
```

## Outcomes

- Introduced variables into our playbook to make it more configurable.
- Refactor redundant tasks into single task step with a loop.
- Define separate sets of tasks for mariadb and mysql, each one applied conditionally based on host context.

## Glossary

- Jinja2: a templating language (like Mustache, Razor, ER or HandlebarsJS)

## Next Steps (Part 4)

Let's look at how to organise our playbooks so that they make sense, and ways of encapsulating things.

```git checkout part-4```

