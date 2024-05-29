## Connect to Server with Username and Password

The following command show's how to authenticate to a server with SSh on a specific port with a username and password. 

`ssh bandit0@bandit.labs.overthewire.org -p 2220`
## Create a key pair

`ssh-keygen id_rsa`

This will create a key pair in the ~/.ssh folder by default

## Copy key pair to a server

`ssh-copy-id -i ~/.ssh/id_rsa user@host`

## Connect to Server with Key

`ssh -i ~/.ssh/mykey user@host`

## Troubleshooting

If trying to login as root, the server might not be configured to allow root logins. Make sure `/etc/sshd_config` includes `PermitRootLogin yes`, `PermitRootLogin prohibit-password`, or `without-password`. If it is set to `forced-commands-only`, the key must be manually configured to use a forced command (see `command=` option in `~/.ssh/authorized_keys`.


#ssh #cli-tool 