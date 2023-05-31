# Setting up SSH to gitlab

## Prerequisites

- git
- ssh-keygen

## Instructions

- #### generate keypair
```
$ ssh-keygen -t ed25519 -C "<comment>"
```

- #### save setting to `~/.ssh/config`
```
# GitLab.com
Host gitlab.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/gitlab-key
```

- #### add the public key to gitlab.com

    user settings -> SSH keys 
