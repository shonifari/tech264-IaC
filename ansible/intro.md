# Setting up Ansible on two VMs

- [Setting up Ansible on two VMs](#setting-up-ansible-on-two-vms)
  - [Create VM](#create-vm)
  - [Update \& upgrade](#update--upgrade)
  - [Install Ansible](#install-ansible)
  - [Add a host](#add-a-host)
    - [Grouping hosts](#grouping-hosts)

## Create VM

Create two VMs as you normally would on AWS, but using the same NSG that has SSH enabled only since their rules align.

## Update & upgrade

`update` and `upgrade` both of our VMs.

## Install Ansible

Use the command `sudo apt-add-repository ppa:ansible/ansible` to download the ansible repo, followed by `sudo apt install ansible -y`.

## Add a host

1. CD into `/etc/ansible` folder.
2. `sudo nano hosts` to edit the **inventory**.
3. Add this to the top of the file:

    ```bash
    [web]
    ec2-app-instance ansible_host=54.195.174.237 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech264-name-aws-key.pem
    ```

4. Use `ansible all -m ping` to ping all the machines that Ansible knows to communicate with.

*Note! We can also put a `[db]` group to specify database dervers.*

  ### Grouping hosts

  1. To put these in a parent group, we set them up like so:

  ```bash
  [test:children]
  web
  db
  ```

This means we could ping the `test` group and get responses from all their children.

**Helpful commands:**

- We can use `ansible-inventory --list` to check groups we've made. Replace `--list` with `--graph` to see it in a tree-like format.
- `ansible web -a (group name or all)` can be used to run a command on a particular machine, or all of them.
- Find more [basic commands here](basics.md)
