# Getting starting with Ansible Lab

@todo resume course

# The LAB

# Exercices



## Tips 

### Vagrant boxes 

For better network performance you can specified an already download box 

```bash
## windows
vagrant box add my-box file:///d:/path/to/file.box

## linux 
vagrant box add my-box /path/to/file.box

## Use box
vagrant init my-box
vagrant up
```

### SSH Errors


SSH key change after destroying an VM for example. 
The fingerprint registered in your known hosts have changed

Example de warning : 
```bash
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
```

Remove Host from your known hosts
```bash
ssh-keygen -f ~/.ssh/known_hosts -R 192.168.33.10

```