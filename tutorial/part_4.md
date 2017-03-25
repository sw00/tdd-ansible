# Test Driven Infrastructure with Ansible

## Part 4: Encapsulation and Roles

Ansible roles are a good way to encapsulate related tasks and data into a reusable components. A role is simply a
playbook's constituent parts organised in a particular directory structure. A role looks like this:

```SHELL
└── rolename
    ├── defaults
    │   └── main.yml
    ├── files
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    │   └── main.yml
    └── vars
        └── main.yml
```

The top-level directory is the name of the role and it has a list of directories as its children: `files`, `handlers`,
`meta`, etc... Ansible consolidates the role by looking in each child directory for a file called `main.yml` that has
the appropriate YAML defined. In other words, `hello_world/tasks/main.yml` might look like this:

```YAML
---
- name: greet world
  shell: echo 'hello cruel world!'
```

So far in writing our playbook we've been acquainted with `tasks` and `vars`, but what are
the other bits?

## Defaults

Defaults are variables that meant to be easily overriden by the playbook that uses the role. These can really be regarded as
the "API" that's exposed to playbooks that might want to use the role. 

## Handlers

Handlers are tasks that are only run when triggererd by other tasks. For example, we might want to ensure a service is
started when it's installed. 

```YAML
- hosts: webserver

tasks:
  - name: install mysqldb
    apt: name=mysql-server state=present
    notify: start mysql service

handlers:
  - name: start mysql service
    service: name=mysql state=started
```

### Files

All the files you want to copy to the target machine should be kept here. This directory is almost exclusively used by the `copy` module.

### Meta

Where you would put the Role's metadata, such as dependencies.

```YAML
---
dependencies:
  - { role: ssl }
```

### Templates

Templates are Jinja2 files that are used by the `template` module to create files on the target machine, interpolated
with appropriate Ansible variables. A basic nginx site configuration might look like this:

```JINJA2
server {
	listen 80 {{ ansible_hostname }};
	listen [::]:80 {{ ansible_hostname }};

	root {{ www_directory }};

	index index.html;

	server_name {{ server_name }};

	location / {
		try_files $uri $uri/ =404;
	}
}
```

### Refactor Database Role

Let's try to refactor the database playbook into a role. Create a `roles` directory with the appropriate structure and a
`site.yml` playbook that will be using the role.


## Outcomes

- Implement the database playbook as an ansible role.
- Create a site.yml playbook that consumes that role.

## Next Steps (Part 5)

Let's look at how we can use the role for testing.

```SHELL
git checkout part-5
```
