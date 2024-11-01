# Setting up Ansible on two VMs

- [Setting up Ansible on two VMs](#setting-up-ansible-on-two-vms)


1. Create two VMs as you normally would on AWS, but using the same NSG that has SSH enabled only since their rules align.
2. When they're both created, we `update` and `upgrade` both of our VMs.
3. Go to the controller VM window and insert the command `sudo apt-add-repository ppa:ansible/ansible` to install the ansible repo, followed by `sudo apt install ansible -y`.
4. Check the version for clarity.
5. CD into `/etc/ansible` folder.
6. Use `ansible all -m ping` to ping all the machines that Ansible knows to communicate with. If there are none, use `sudo nano hosts` once in the `/etc/ansible` directory to add some.
7. Add this to the top of the file:

```bash
[web]
ec2-app-instance ansible_host=54.195.174.237 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech264-name-aws-key.pem
```

This will then allow the ping function to work.

*Note! We can also put a `[db]` group to specify database dervers.*

8. To put these in a parent group, we set them up like so:

```bash
[test:children]
web
db
```

This means we could ping the `test` group and get responses from all their children.

**Helpful commands:**

- We can use `ansible-inventory --list` to check groups we've made. Replace `--list` with `--graph` to see it in a tree-like format.
- `ansible web -a (group name or all)` can be used to run a command on a particular machine, or all of them.

1. Insert `sudo nano install_nginx.yaml` to create a yaml file.
2. In the file, insert this:

```bash
# Starts with --- (three hyphens)
---
 
# Name of the play
- name: install nginx play
  # Where - on which devices - run this playbook
  hosts: web
 
  # Get comprehensive facts on the hosts / devices
  gather_facts: yes # If you want the playbook to run faster, turn this off using "no"
 
  # Do we need to provide admin access? Use sudo
  become: true
 
  # Instructions for this play, known as "tasks"
  # First Task: Install nginx on the Target Node
  tasks:
  - name: install and configure nginx
    # Use "nginx" package // "state=present" means we need it running
    apt: pkg=nginx state=present
```

11. We can use `ansible-playbook install_nginx.yaml` to activate the playbook. This should mean that `nginx` is installed and running.
