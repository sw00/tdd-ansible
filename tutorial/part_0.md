## Part 0: Vagrantfile

[Vagrant](http://vagrantup.com) is a tool that allows us to specify virtual machine configurations in code. You will
notice there is a `Vagrantfile` in this repo. It's used to describe and provision virtual machines in a standardised
way.

In our `Vagrantfile`, we've specified two virtual machines, namely `control` and `webserver`. `control` is the machine
that will be provisioned with [Ansible](https://docs.ansible.com/) and `webserver` will be target machine under
configuration management.

At this point, Ansible is not supported on Windows at this point so we'll be using `control` to manage `webserver`.

Let's start up our virtual machines. Execute the following in the command line:

```
vagrant up
```

The command above should do the following:

1. Create 2 virtual machines named `control` and `webserver`.
2. Assign a private network IP address to them of `192.168.33.10` and `192.168.33.20` to them respectively.
3. Install Ansible on `control`.

To test that the above command worked, we'll [SSH](https://en.wikipedia.org/wiki/Secure_Shell) into `control` machine
and try to execute Ansible. 

Type `vagrant ssh control` into your terminal and you should be presented with a prompt
like `ubuntu@control$`. You are now in the `control` machine.

Now type `ansible --version` and you should be greeted with the following output:
```
ansible 2.2.1.0
config file = /etc/ansible/ansible.cfg
configured module search path = Default w/o overrides
```

You can read more about Vagrant and the features it provides in its [documentation](https://www.vagrantup.com/docs/).

## Outcomes

- Provisioned two virtual machines (`control` and `webserver`).
- Machine called `control` has Ansible installed on it.

## Concepts

- Vagrant: a tool that allows us to specify virtual machines in a file.
- Controller Machine: the machine where Ansible is installed, responsible for managing the servers under configuration management.

## Next Steps (Part 1)

```
git checkout part-1
```

