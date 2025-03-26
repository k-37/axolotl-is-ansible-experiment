# Introduction

This repository is used to practice [Ansible](https://en.wikipedia.org/wiki/Ansible_(software)). Commit messages will have detailed description which commands, documentation and other resources were used along the way. The goal is to have information on *why* and *how* in addition to *what* has changed. [`git blame`](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-blame) and `git log` can be used to follow along, commit history can be seen as a tutorial. Commit Driven Learning (CDL). 🤯

ᓬ(ᵔ⤙ᵔ๑)ᕒ

# Requirements

- Server with minimal installation of [Debian 12](https://www.debian.org/) and with [SSH enabled](https://phoenixnap.com/kb/how-to-enable-ssh-on-debian). In this project we will refer to it as **remote server**, it will be used as both Ansible control node and managed host.

> [!NOTE]
> Ansible is not usually installed on the same host which is configured by it. This approach is used so we can do everything by using a single server with minimal dependencies on our development machine.

> [!NOTE]
> The Ansible project is developed on [Debian 12](https://www.debian.org/) if you use another OS have that in mind if something doesn't work as expected.

# Getting started

We can install and use Ansible as an APT package, with [`pipx`](https://github.com/pypa/pipx#overview-what-is-pipx) or use [Execution Environments](https://docs.ansible.com/ansible/latest/getting_started_ee/index.html) as an alternative approach.

## Option 1. Install and use Ansible as an APT package

Login to the remote server and install Ansible:

    sudo apt-get update
    sudo apt-get install ansible sshpass rsync

### Usage with APT

Copy contents of the [`./ansible`](ansible) folder from the current repository to `${HOME}/ansible` on the remote server or use [auto sync](doc/auto_sync.md).

On the remote server execute command:

    cd "${HOME}/ansible/playbooks"

Install dependencies:

    ansible-galaxy collection install -r requirements.yml

> [!NOTE]
> On the next steps, after entering your `SSH password` and `BECOME password` for the remote server, you will be asked for `Vault password`, it is `axolotl`.

Verify inventory:

    ansible-inventory --ask-vault-pass --list

Ping the `axolotl` host in the inventory:

    ansible axolotl -m ping --ask-pass --ask-become-pass --ask-vault-pass

Run playbook:

    ansible-playbook --ask-pass --ask-become-pass --ask-vault-pass main.yml

## Option 2. Install and use Ansible with `pipx`

Login to the remote server and install `pipx`:

    sudo apt-get install rsync sshpass pipx

Install a specific version of `ansible-core`:

    pipx install ansible-core==2.14.8

Update `PATH` environment variable for the currently logged in user:

    pipx ensurepath

Open a new terminal or re-login for the `PATH` changes to take effect.

Verify your environment with the following command:

    ansible --version

### Usage with `pipx`

Usage is the same as when [using Ansible with APT](#usage-with-apt).

## Option 3. Use Execution Environment (EE)

Install dependencies on the remote server:

    sudo apt-get update
    sudo apt-get install rsync sshpass podman pipx

Install `ansible-navigator` and `ansible-builder`:

    pipx install ansible-navigator
    pipx install ansible-builder

Update `PATH` environment variable for the currently logged in user:

    pipx ensurepath

Open a new terminal or re-login for the `PATH` changes to take effect.

Verify your environment with the following commands:

    ansible-navigator --version
    ansible-builder --version

### Usage with EE

Copy contents of the [`./ansible`](ansible) folder from the current repository to `${HOME}/ansible` on the remote server or use [auto sync](doc/auto_sync.md).

On the remote server execute command:

    cd "${HOME}/ansible/playbooks"

Build an EE container image called `fedora_ee` (**NOTICE**: it requires ~25 GiB of free space on `/home` partition):

    ansible-builder build --tag fedora_ee

In [`./ansible/inventories/hosts.yml`](ansible/inventories/hosts.yml) set IP address of the remote server, by modifying `ansible_host` variable, which is accessible outside of the container (i.e. not `127.0.0.1` or `localhost`).

> [!NOTE]
> On the next step, after entering your `SSH password` and `BECOME password` for the remote server, you will be asked for `Vault password`, it is `axolotl`.

Run the playbook inside the `fedora_ee` EE (**NOTICE**: `<REMOTE_USER>` must be replaced in the command):

    ansible-navigator run main.yml --execution-environment-image fedora_ee --mode stdout --pull-policy missing --enable-prompts --ask-pass --ask-become-pass --ask-vault-pass -u <REMOTE_USER>

# Usage

## Web server

After executing Ansible playbook on remote server, on development machine in `sudo vi /etc/hosts` file add:

    <IP_ADDRESS_OF_REMOTE_SERVER> wp.example.com hello.example.com

In [Mozilla Firefox](https://www.mozilla.org/firefox/) open URLs:

- [https://wp.example.com/](https://wp.example.com/) which hosts local instance of [WordPress](https://en.wikipedia.org/wiki/WordPress).
- [https://hello.example.com/](https://hello.example.com/) which hosts simple Hello World app made with [Node.js](https://en.wikipedia.org/wiki/Node.js).

> [!NOTE]
> Warning for self-signed certificate is expected.

## Database

To connect to [MariaDB](https://en.wikipedia.org/wiki/MariaDB) on the remote server execute command:

    mariadb -u root -p

Database password is stored in [`./ansible/inventories/host_vars/axolotl/003-mariadb-secrets-encrypted.yml`](ansible/inventories/host_vars/axolotl/003-mariadb-secrets-encrypted.yml) but it is encrypted. [Ansible Vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html) was used for encryption, `Vault password` is `axolotl`, to decrypt on the remote server execute:

    cd "${HOME}/ansible/inventories/host_vars/axolotl"
    cat 003-mariadb-secrets-encrypted.yml # Check that file is encrypted
    ansible-vault decrypt 003-mariadb-secrets-encrypted.yml
    cat 003-mariadb-secrets-encrypted.yml

To encrypt them again with new password:

    cat 003-mariadb-secrets-encrypted.yml # Check that file is decrypted
    ansible-vault encrypt 003-mariadb-secrets-encrypted.yml
    cat 003-mariadb-secrets-encrypted.yml

After that, to list all databases:

    SHOW DATABASES;

# Documentation

- [Auto sync](doc/auto_sync.md).
