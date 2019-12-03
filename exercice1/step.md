# Ansible basics

* boot 2 vm
* vm with shell script
* sync folder

Init Vagrant box

```bash
vagrant init hashicorp/bionic64

```

Configure box

```bash
Vagrant.configure("2") do |config|
    # Configure web server machine
     config.vm.define "web" do |web1|
         web1.vm.box = "ubuntu/bionic64"
         web1.vm.network "private_network", ip: "192.169.31.10"
         web1.vm.provision :shell do |shell|
             shell.path = "web.sh"
         end
     end
end
```


```bash
#!/bin/bash
 sudo apt-get update
 sudo apt-get install -y nginx
 touch /var/www/html/index.html
 echo "Hello !" >> /var/www/html/index.html
```


```bash
vagrant up

```

If changes needed :

```bash
 vagrant up --provision
```


## Sync folder

Sync Folders

```bash
    config.vm.synced_folder ".", "/home/vagrant/files"s
```


## Define port forwarding

Connect to vagrant box

```bash
vagrant ssh
```

Define host port to 8000

```
    web.vm.network "forwarded_port", guest: 80, host: 8000
```

## Ansible commands

Ping machine

```bash
ansiblei 192.169.31.10 -m ping
```

Restart nginx

```bash
ansiblei 192.169.31.10 -m service -a "name=nginx state=restarted"

```


## Use ansible playbook

Replace shell script to use ansible provision :

```bash
config.vm.provision "ansible_local" do |ans|
    ans.playbook = "vagrant_playbook.yml"
    ans.install = true
    ans.install_mode = "pip"
end

```


## Templates


## vars