# Basic Ansible Commands

Ansible is a powerful automation tool used to manage and configure systems. Below are some of the basic commands you need to get started with Ansible.

## 1. Checking Ansible Version

To check the installed version of Ansible:

```sh
ansible --version
```

## 2. Ping All Hosts

To verify connectivity to all hosts in your inventory:

```sh
ansible all -m ping
```

## 3. Running Ad-Hoc Commands

Ad-hoc commands allow you to run simple tasks without writing a playbook.

### Example: Rebooting Hosts

```sh
ansible all -a "/sbin/reboot"
```

### Example: Running a Command

```sh
ansible all -m command -a "uname -a"
```

## 4. Managing Users

You can manage users on remote hosts using the `user` module.

### Example: Creating a New User

```sh
ansible all -m user -a "name=ansible password=<encrypted_password>"
```

### Example: Deleting a User

```sh
ansible all -m user -a "name=ansible state=absent"
```

## 5. Gathering Facts

To gather information about your hosts:

```sh
ansible all -m setup
```

## 6. Running Playbooks

Playbooks are YAML files that define a series of tasks to be executed on your hosts.

### Example: Running a Playbook

```sh
ansible-playbook playbook.yml
```

## 7. Managing Inventory

Ansible uses an inventory file to manage and organize hosts.

### Example: Listing Hosts in Inventory

```sh
ansible all --list-hosts -i inventory
```

## 8. Checking Syntax

To check the syntax of a playbook:

```sh
ansible-playbook playbook.yml --syntax-check
```

## 9. Dry Run

To perform a dry run (test run) of a playbook:

```sh
ansible-playbook playbook.yml --check
```

## 10. Viewing Help

To view help for any Ansible command:

```sh
ansible <command> --help
```

These commands should help you get started with basic Ansible operations. For more detailed information, you can refer to the [Ansible documentation](https://docs.ansible.com/ansible/latest/getting_started/index.html)¹²³.

¹: [Getting started with Ansible](https://docs.ansible.com/ansible/latest/getting_started/index.html)
²: [Run Your First Command and Playbook](https://docs.ansible.com/ansible/latest/network/getting_started/first_playbook.html)
³: [Ansible Commands | Concepts | Basic And Advanced Commands](https://www.educba.com/ansible-commands/)
