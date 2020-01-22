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


### Error urllib3

```bash
/usr/lib/python3/dist-packages/requests/__init__.py:91: RequestsDependencyWarning: urllib3 (1.25.3) or chardet (3.0.4) doesn't match a supported version!
```

Update your python lib

```bash
pip install --upgrade --user urllib3==1.24.3
```

### Python 3 support on remote 

if using python 3 on remote machine, you must provide [python interpreter](https://docs.ansible.com/ansible/latest/reference_appendices/python_3_support.html) to commands or inventory files

```bash
ansible_python_interpreter=/usr/bin/python3
```


### cannot conect to host via ssh 


connect to "defaut"

```
ssh vagrant@127.0.0.1 -p 2222 -i .vagrant/machines/default/virtualbox/private_key
```