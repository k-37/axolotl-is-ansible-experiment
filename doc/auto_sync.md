# Auto sync

Auto sync of the `ansible` folder from the current repository on the development machine to `${HOME}/ansible` on the remote server.

# Requirements

On development machine with Debian/Ubuntu:

    sudo apt-get install rsync sshpass inotify-tools

`rsync` needs to be installed on both hosts (source and destination).

# Command

The command will auto sync changes.

Replace all `<...>` with appropriate values for the remote server.

This is a single command, execute it **from the project root** on your development machine:

    while inotifywait -r ./ansible/*; do \
        sshpass -p '<PASSWORD_FOR_SSH>' \
        rsync \
        -v --compress --recursive --times --delete \
        ./ansible <REMOTE_USER>@<REMOTE_HOST_NAME_OR_IP_ADDRESS>:/home/<REMOTE_USER>; \
        date; \
    done
