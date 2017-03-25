# Test Driven Infrastructure with Ansible

## Part 1: Ansible host inventory

Ansible uses a host inventory file to keep track of hosts it needs to connect to. By default, Ansible looks in
`/etc/ansible/hosts`. We're going to override this with our own host inventory that tells our `control` machine about
`webserver`.

Lets move `/etc/ansible/hosts` out of the way. Run the following in the `control` machine:

```SHELL
sudo mv /etc/ansible/hosts /etc/ansible/hosts.bak
```

Now can create a file describing our webserver and the IP address it can be reached at. Open up a text editor and create
a new `hosts` file with the following contents.

```INI 
[webserver]
192.168.33.20
```

Next, let's move it in place: `mv hosts /etc/ansible/hosts`.

We can test if ansible picks up our new host inventory by executing the following:

```SHELL 
$ansible all --list-hosts hosts (1): 192.168.33.20 
```

The ansible command accepts a [host pattern](https://docs.ansible.com/ansible/intro_patterns.html) as the first
argument. In our case, `all` will match all hosts in the inventory. This is useful for limiting particular groups of
hosts we execute tasks on.

Let's try to run a task on our `webserver` machine. Ansible uses "modules" to accomplish tasks, which are small
components that help manage aspects of a server like users, files & directories, services, etc... Ansible has a module
called `setup` which collects _facts_ about the machine it connects to. Let's try to run the `setup` module against our
`webserver` machine.

```SHELL
$ansible webserver -m setup
```

You should see a very large output of information in JSON. These are all facts that the `setup` module discovered about
the machine Ansible ran it on.

Another module is `ping`. What do you think it does?

## Outcomes

- Define a host inventory for our target `webserver`.
- Test the inventory by running an ad-hoc Ansible command.
- See the outout of ansible "setup" and "ping" modules.

## Glossary

- Inventory: an INI file that specifies the hosts that Ansible should manage.
- Module: the components Ansible uses to accomplish tasks.
- Host Pattern: how Ansible decides which host to run tasks against.
- Facts: information that is discovered about a system or server.

## Next Steps (Part 2)

So far we've been using Ansible in a very interactive, ad-hoc manner. This is useful for orchestration and running
arbitrary tasks but how can we describe our infrastructure and manage it under version control?

```git checkout part-2```
