# Vagrant and Ansible Essentials

@todo : Vagrant + ansible

## What you will learn

* init local env for using ansible
* boot 2 vm
* vm with shell script
* sync folder

## What you will build

@todo : schema

## What you Need

@todo



## Vagrant 

### configure boxes

Init Vagrant file with a box
 
```bash
vagrant init hashicorp/bionic64
```

Nb: you can use another recommanded box provider "bento"

```bash
vagrant init bento/centos-7.7
```

Run box

```bash
vagrant up
```

### Connect to your vm with SSH


```bash
vagrant ssh
```

### Static  IP

```bash
Vagrant.configure("2") do |config|
    # Configure web server machine
     config.vm.define "web" do |web1|
         web1.vm.box = "bento/centos-7.7"
         web1.vm.network "private_network", ip: "192.169.10.10"
     end
end
```

Restart vm

```bash
vagrant reload
```

or equivalent 

```bash
vagrant halt
vagrant up
```

Ping machine

```bash
ping -c 3 192.169.10.10
```

### Provisoning With shell script
Basic shell provisioning :


```bash
#!/bin/bash
 sudo apt-get update
 sudo apt-get install -y nginx
 touch /var/www/html/index.html
 echo "Hello !" >> /var/www/html/index.html
```

```bash
Vagrant.configure("2") do |config|
    # Configure web server machine
     config.vm.define "web" do |web1|
         web1.vm.box = "bento/centos-7.7"
         web1.vm.network "private_network", ip: "192.169.10.10"
         web1.vm.provision :shell do |shell|
             shell.path = "web.sh"
         end
     end
end
```

Restart vm 
> provisioning is done once by default. We force it with arg --provision

```bash
vagrant reload --provision
```


### Sync vm folder with local folder

Add current directory to vm 

```bash
    web1.vm.synced_folder ".", "/home/vagrant/files"s
```

Reload vagrant 

```bash
vagrant reload
```


### Define port forwarding

Forwar host port 8000 to vm guest port 80

```
    web.vm.network "forwarded_port", guest: 80, host: 8000
```

Reload vagrant 

```bash
vagrant reload
```

## Ansible commands

> @todo  add -u vagrant ?

### Modules

Use Ping module for all machine with all parameters specified

```bash
ansible all -i '192.168.33.10,'  -m ping -u vagrant  --private-key=./.vagrant/machines/default/virtualbox/private_key 
```
> Nb: private key location change with your vagrant box configurations


Define inventory file 'hosts'

```bash
192.168.33.10 ansible_ssh_user=vagrant ansible_ssh_private_key_file='.vagrant/machines/default/virtualbox/private_key'
```

Ping using inventory 

```bash
ansible all -i hosts  -m ping 
```


Get infos about machine 

```bash
ansible all -i hosts  -m setup
```

> Tips: use Sed and Jq to extract infos
> ansible all -i hosts -m setup | sed '1c {'|jq 


Restart nginx

```bash
ansible all -i hosts -m service -a "name=nginx state=restarted"

```


### Use vagrant with ansible playbook

Replace shell script to use ansible provision :

```bash
config.vm.provision "ansible_local" do |ans|
    ans.playbook = "playbook.yml"
    ans.install = true
    ans.install_mode = "pip"
end
```

### Use ansible without vagrant provider

Replace shell script to use ansible provision :

@todo



## Exercice

* Start a VM with IP 192.169.10.99
* Provision VM with Nginx server
* Serve local HTML files with VM server


