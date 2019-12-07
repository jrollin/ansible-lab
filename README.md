# Getting starting with Ansible Lab

![Vagrant](./assets/vagrant-logo.png)
![Ansible](./assets/red-hat-ansible-logo.png)


Pratice Ansible with virtual machines (vagrant)


## Labs

* [Essentials](1_essentials/README.md)
* [Web app](2_webapp/README.md)
* [gitlab + ci + cd](3_gitlab_ci_cd/README.md)

## Tools used

* Virtualbox : 6.x
* Vagrant : 2.2.x
* Ansible 2.9.x


## Notes / Tips 

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