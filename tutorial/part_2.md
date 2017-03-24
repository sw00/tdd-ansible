# Test Driven Infrastructure with Ansible

- 

### Non-task

## Part 2: Playbooks

Playbooks are files that specify multiple Tasks to be run along with additionally functionality we wouldn't be able to
achieve with ad-hoc Ansible commands. Playbooks are specified in
[YAML](https://docs.ansible.com/ansible/YAMLSyntax.html) format which is just a data serialization format that's easy to
read for humans. Think of it as JSON but without all the curly braces and brackets.

Lets create a playbook that will install MariaDB for us on our `webserver`. Create a file called `database.yml` that
looks like below.

```YAML
---
- hosts: all
  become: yes
  tasks:
    - name: install mysqldb
      apt: name=mysql-server state=present update_cache=yes
```

This is functionally equivalent to this ad-hoc command:

```SHELL
ansible all -b -m apt -a 'name=mysql-server state=present update_cache=true'
```

To run the playbook, we'll use a slightly different command than before. Let's run it by executing the following
command.

```SHELL
ansible-playbook database.yml
```

You should see the following output which describes the tasks that have been run.

```SHELL
PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
ok: [192.168.33.20]

TASK [install mariadb] *********************************************************
changed: [192.168.33.20]

PLAY RECAP *********************************************************************
192.168.33.20              : ok=2    changed=1    unreachable=0    failed=0
```

As you can see, the output from `ansible-playbook` is somewhat different to running `ansible`. Ansible playbooks allow
you to define multiple tasks in sequence.

Try to modify your `database.yml` playbook to create a MySQL database and user for our application. You will need the
following two modules: [mysql_db](https://docs.ansible.com/ansible/mysql_db_module.html) and
[mysql_user](https://docs.ansible.com/ansible/mysql_user_module.html).

```YAML
---
- hosts: all
  become: yes
  tasks:
    - name: install mariadb
      apt: name=mariadb-server state=present update_cache=yes

    - name: create database `codedinbraam`
      mysql_db: name=codedinbraam state=present

    - name: create user
      mysql_user:
        name: dev
        password: dev.password
        priv: 'codedinbraam.*:ALL'
        state: present
```

### Caveat

You'll find that running the playbook above will result in an error.

```SHELL
fatal: [192.168.33.20]: FAILED! => {"changed": false, "failed": true, "msg": "the python mysqldb module is required"}
```

This is because certain modules require dependencies to be installed on the target machine in order for them to work. In
the case of `mysql_db` and `mysql_user`, they require the `python-mysql` to be present on the target machine. Luckily
you can simply add the dependency as a task before the appropriate module is run.

```YAML
- name: install ansible module dependencies
  apt: name=python-mysqldb state=present
```

## Outcomes

- Created an Ansible playbook that defines mysqldb, database and user to be present on host.
- Run playbook against `webserver` successfully.

## Glossary

- Playbook: a file describing hosts and set of tasks to apply to the host.

## Next Steps (Part 3)

So far so good, but how do make our playbook configurable?

```SHELL
git checkout part-3
```
