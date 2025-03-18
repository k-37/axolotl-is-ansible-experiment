# Introduction

This repository is used to practice [Ansible](https://en.wikipedia.org/wiki/Ansible_(software)). Commit messages will have detailed description which commands, documentation and other resources were used along the way. The goal is to have information on *why* and *how* in addition to *what* has changed. [`git blame`](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-blame) and `git log` can be used to follow along, commit history can be seen as a tutorial. Commit Driven Learning (CDL). 🤯

ᓬ(ᵔ⤙ᵔ๑)ᕒ

# Requirements

- Server with minimal installation of [Debian 12](https://www.debian.org/) and with [SSH enabled](https://phoenixnap.com/kb/how-to-enable-ssh-on-debian).

# Getting started

We can install and use Ansible as an APT package or use [Execution Environments](https://docs.ansible.com/ansible/latest/getting_started_ee/index.html) as an alternative approach.

## Option 1. Install and use Ansible as an APT package

Login to the server and install Ansible:

    sudo apt-get update
    sudo apt-get install ansible sshpass rsync

> [!NOTE]
> Ansible is not usually installed on the same host which is configured by it. This approach is used so we can do everything by using a single server.

Copy contents of the `./ansible` folder from the current repository to `${HOME}/ansible` on the server, this command will auto sync changes (replace all `<...>` with appropriate values for your systems):

    while inorifywait -r <DIRECTORY_CONTAINING_CURRENT_REPOSITORY>/ansible/*; do sshpass -p '<PASSWORD_FOR_SSH>' rsync -v --compress --recursive --times --delete <DIRECTORY_CONTAINING_CURRENT_REPOSITORY>/ansible <[USER@]HOST:/home/USER>; date; done

On the server execute command:

    cd "${HOME}/ansible/playbooks"

Verify inventory:

    ansible-inventory --list

Ping the `axolotl` host in the inventory:

    ansible axolotl -m ping --ask-pass

Run playbook:

    ansible-playbook --ask-pass main.yml

## Option 2. Use Execution Environment (EE)

Install dependencies:

    sudo apt-get update
    sudo apt-get install rsync sshpass podman python3 python3-pip pipx

Install `ansible-navigator` and `ansible-builder`:

    pipx install ansible-navigator
    pipx install ansible-builder

Update your `PATH` environment variable:

    pipx ensurepath

Open a new terminal or re-login for the `PATH` changes to take effect.

Verify your environment with the following commands:

    ansible-navigator --version
    ansible-builder --version

Copy contents of the `./ansible` folder from the current repository to `${HOME}/ansible` on the server, this command will auto sync changes (replace all `<...>` with appropriate values for your systems):

    while inorifywait -r <DIRECTORY_CONTAINING_CURRENT_REPOSITORY>/ansible/*; do sshpass -p '<PASSWORD_FOR_SSH>' rsync -v --compress --recursive --times --delete <DIRECTORY_CONTAINING_CURRENT_REPOSITORY>/ansible <[USER@]HOST:/home/USER>; date; done

On the server execute command:

    cd "${HOME}/ansible/playbooks"

Build an EE container image called `fedora_ee` (it requires ~25 GiB of free space on `/home` partition):

    ansible-builder build --tag fedora_ee

In `./ansible/inventories/hosts` set IP address of the managed host which is accessible outside of the container (i.e. not `127.0.0.1` or `localhost`).

Run the playbook inside the `fedora_ee` EE (note that `<REMOTE_USER>` must be replaced in the command):

    ansible-navigator run main.yml --execution-environment-image fedora_ee --mode stdout --pull-policy missing --enable-prompts --ask-pass -u <REMOTE_USER>
