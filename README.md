# Introduction

This repository is used to practice [Ansible](https://en.wikipedia.org/wiki/Ansible_(software)). Commit messages will have detailed description which commands, documentation and other resources were used along the way. The goal is to have information on *why* and *how* in addition to *what* has changed. [`git blame`](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-blame) and `git log` can be used to follow along, commit history can be seen as a tutorial. Commit Driven Learning (CDL). 🤯

ᓬ(ᵔ⤙ᵔ๑)ᕒ

# Requirements

- Server with minimal installation of [Debian 12](https://www.debian.org/) and with [SSH enabled](https://phoenixnap.com/kb/how-to-enable-ssh-on-debian).

# Getting started

Login to the server and install Ansible:

    sudo apt-get update
    sudo apt-get install ansible sshpass

> [!NOTE]
> Ansible is not usually installed on the same host which is configured by it. This approach is used so we can do everything by using a single server.

Copy contents of the `./ansible` folder from the current repository to `${HOME}/ansible` on the server.

On the server execute command:

    cd "${HOME}/ansible/playbooks"

Verify inventory:

    ansible-inventory --list

Ping the `axolotl` host in the inventory:

    ansible axolotl -m ping --ask-pass

Run playbook:

    ansible-playbook --ask-pass main.yml
