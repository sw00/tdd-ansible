# Test Driven Infrastructure with Ansible

## Part 4: Test Playbook

Mistakes can be expensive when automating infrastructure, so how can we get catch them earlier? Testing, like in
software development is important for us to get quick feedback on changes we make and validating our code.

Ansible already has some useful behaviour out of the box, like stopping the run if a task fails. But we should be able
to validate other things like:

- Syntax: Are all the YAML files formatted correctly?
- Can the role apply all the tasks successfully?
- Is the role [idempotent](https://stackoverflow.com/questions/1077412/what-is-an-idempotent-operation)?
- Does the role do what it's supposed to?

### The Test Playbook

Let's create a test playbook that'll apply our `database` role as well as a test inventory.

```SHELL
roles/
tests/
  test.yml <-- your test playbook
  inventory <-- an inventory file to use with the playbook
```

```INI
# test inventory
localhost
```

In the playbook we wan to use our database role.

```YAML
---
- hosts: localhost
  become: yes
  roles:
    - database
```

### Syntax Check

In order to run a syntax check, ansible gives us the `--syntax-check` flag.

```
ansible-playbook -i tests/inventory tests/test.yml --syntax-check
```

### First-Run

To see if the role runs successfully the first time, you can execute the playbook itself.

```
ansible-playbook -i tests/inventory tests/test.yml --connection=local --sudo
```

In this case, we're applying the playbook to the control machine itself, but in real life you'd want to spin up a brand
new server to test the playbook against.

### Idempotence

To ensure that nothing changes the second time (or n times its run afterwards), we simple run the playbook again and
assert that `changed` and `failed` both report 0.

```SHELL
# Run the role/playbook again, checking to make sure it's idempotent.
- >
  ansible-playbook -i tests/inventory tests/test.yml --connection=local --sudo
  | grep -q 'changed=0.*failed=0'
  && (echo 'Idempotence test: pass' && exit 0)
  || (echo 'Idempotence test: fail' && exit 1)
```

### Correctness

Last thing we can check is that the role did what it's supposed to do. This step will depend on what the role is, in the case of a databse role, we want to make sure that MySQL is up and running on the correct port. In the case of a webserver, we can simply curl it. It depends on how what will give you confidence in you role.


## Outcomes

- Create a test playbook to validate our role.


## Next Steps (Part 6):


