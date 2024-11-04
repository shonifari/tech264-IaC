# Ansible Playbook Documentation

This Ansible playbook provisions an application VM by performing several tasks, including cloning a repository, installing Node.js, and starting the application using PM2.

- [Ansible Playbook Documentation](#ansible-playbook-documentation)
  - [Playbook Structure](#playbook-structure)
    - [Task Breakdown](#task-breakdown)
      - [**Clone the App Repository**](#clone-the-app-repository)
      - [**Update apt Repository and Cache**](#update-apt-repository-and-cache)
      - [**Install NodeSource Node.js 20.x Repository**](#install-nodesource-nodejs-20x-repository)
      - [**Install Node.js and npm**](#install-nodejs-and-npm)
      - [**Verify and print Node.js Version**](#verify-and-print-nodejs-version)
      - [**Install PM2**](#install-pm2)
      - [**Copy App Folder to Target Node**](#copy-app-folder-to-target-node)
      - [**Install Packages Based on package.json**](#install-packages-based-on-packagejson)
      - [**Start the Application Using PM2**](#start-the-application-using-pm2)

## Playbook Structure

```yaml
---
- name: Provision app vm
  hosts: web
  gather_facts: yes
  become: true

  tasks:

    # ALL TASKS BELOW

```

### Task Breakdown

#### **Clone the App Repository**

    ```yaml
    - name: Clone the app repository to the controller node
        delegate_to: localhost
        ansible.builtin.git:
            repo: 'https://github.com/shonifari/tech264-sparta-app.git'
            dest: /home/{{ ansible_user }}/repo
            version: main
    ```

- **Task**: Clones the application repository to the controller node.
- **Module**: `ansible.builtin.git`
- **Details**:
  - `repo`: The URL of the Git repository.
  - `dest`: The destination directory on the controller node.
  - `version`: The branch to clone (main).

#### **Update apt Repository and Cache**

    ```yaml
    - name: Update apt repository and cache
      apt:
        update_cache: yes
        force_apt_get: yes
        cache_valid_time: 3600
    ```

- **Task**: Updates the apt package repository and cache.
- **Module**: `apt`
- **Details**:
  - `update_cache`: Ensures the package list is updated.
  - `force_apt_get`: Forces the use of `apt-get`.
  - `cache_valid_time`: Sets the cache validity time.

#### **Install NodeSource Node.js 20.x Repository**

    ```yaml
    - name: Install NodeSource Node.js 20.x repository
      ansible.builtin.shell: |
        curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

    ```

- **Task**: Adds the NodeSource repository for Node.js 20.x.
- **Module**: `ansible.builtin.shell`
- **Details**: Runs a shell command to add the repository.

#### **Install Node.js and npm**

    ```yaml
    - name: Install Node.js and npm
      apt:
        name:
          - nodejs
        state: present
    ```

- **Task**: Installs Node.js and npm.
- **Module**: `apt`
- **Details**:
  - `name`: Specifies the packages to install.
  - `state`: Ensures the packages are present.

#### **Verify and print Node.js Version**

    ```yaml
    - name: Verify Node.js version
      ansible.builtin.command: node -v
      register: node_version

    - name: Print Node.js version
      ansible.builtin.debug:
        msg: "Node.js version is {{ node_version.stdout }}"
    ```

- **Task**: Verifies the installed Node.js version.
- **Module**: `ansible.builtin.command`, `ansible.builtin.debug`
- **Details**: Runs the `node -v` command, registers the output and prints.

#### **Install PM2**

    ```yaml
    - name: Install PM2
      npm:
        name: pm2
        global: yes
        state: present
    ```

- **Task**: Installs PM2 globally using npm.
- **Module**: `npm`
- **Details**:
  - `name`: Specifies the package to install (pm2).
  - `global`: Ensures the package is installed globally.
  - `state`: Ensures the package is present.

#### **Copy App Folder to Target Node**

    ```yaml
    - name: Copy app folder to target node
      ansible.builtin.copy:
        src: /home/{{ ansible_user }}/repo/
        dest: /home/{{ ansible_user }}/repo/
        mode: '0755'
    ```

- **Task**: Copies the application folder to the target node.
- **Module**: `ansible.builtin.copy`
- **Details**:
  - `src`: Source directory on the controller node.
  - `dest`: Destination directory on the target node.
  - `mode`: Sets the permissions for the copied files.

#### **Install Packages Based on package.json**

    ```yaml
    - name: Install packages based on package.json
      community.general.npm:
        path: /home/{{ ansible_user }}/repo/app/

    ```

    - **Task**: Installs npm packages based on the `package.json` file.
    - **Module**: `community.general.npm`
    - **Details**:
      - `path`: Path to the application directory containing `package.json`.

#### **Start the Application Using PM2**

    ```yaml
    - name: Start the application using PM2
      shell: pm2 start app.js
      args:
        chdir: /home/{{ ansible_user }}/repo/app
    ```

    - **Task**: Starts the application using PM2.
    - **Module**: `shell`
    - **Details**:
      - `shell`: Runs the `pm2 start app.js` command.
      - `args`: Changes the working directory to the application directory.

This documentation provides a detailed explanation of each task in the playbook, ensuring clarity and understanding of the provisioning process. Let me know if you need any further details!
