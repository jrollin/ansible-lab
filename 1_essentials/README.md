# Vagrant and Ansible Essentials

Lean how to setup local environnement with Vagrant
Understand ansible commands and configuration management

## What you will learn

* setup vagrant boxes 
* configure ansible with vagrant
* define ansible tasks 
* sync folder

## What you will build

An HTTP server with Nginx to serve local HTML file


## Vagrant 

### configure boxes

Init Vagrant file with a box
 
```bash
vagrant init hashicorp/bionic64
```

Nb: you can use another recommanded box provider "bento"

```bash
vagrant init bento/centos-7.7
vagrant init bento/ubuntu-18.10
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

```ruby
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

Debian like

```bash
#!/bin/bash
sudo apt-get update
sudo apt-get install -y nginx
mkdir -p /var/www/html/
touch /var/www/html/index.html
 echo "Hello !" >> /var/www/html/index.html
```

Centos 

```bash
#!/bin/bash
service httpd stop
sudo yum update
sudo yum install -y epel-release nginx 
mkdir -p /var/www/html/
touch /var/www/html/index.html
echo "Hello !" >> /var/www/html/index.html
sudo systemctl start nginx
```

```ruby
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

```ruby
web1.vm.synced_folder ".", "/home/vagrant/files"s
```

Reload vagrant 

```bash
vagrant reload
```


### Define port forwarding

Forwar host port 8000 to vm guest port 80

```ruby
web.vm.network "forwarded_port", guest: 80, host: 8000
```

Reload vagrant 

```bash
vagrant reload
```

## Ansible commands


Notes about using vagrant with ansible :

* ssh keys are already provisionned by vagrant when creating a box
* the ssh user is vagrant
* ssh private key is located in '.vagrant/machines/<VM_NAME>/virtualbox/private_key' 
* ssh port is 2222
  
Tips to know ports 

```bash
vagrant port
```

### Ansible modules

Use Ping module for all machine with all parameters specified

```bash
ansible all -i '192.168.33.10,' -m ping -u vagrant  --private-key=./.vagrant/machines/default/virtualbox/private_key 
```
> Nb: this method is not the "ansible way", you should use an inventory file
> Nb: note the "," required with inline inventory


Define inventory file 'hosts' and add this line

```bash
192.168.33.10 ansible_ssh_user=vagrant ansible_ssh_private_key_file='.vagrant/machines/default/virtualbox/private_key'
```

Use ping module using inventory 

```bash
ansible all -i hosts  -m ping 
```

> Nb: we did not defined sections in our inventory file so we use 'all'


Use Setup module to get infos about vm 

```bash
ansible all -i hosts  -m setup
```

> Tips: use Sed and Jq to extract infos
> ansible all -i hosts -m setup | sed '1c {'|jq 


Use module apt to install Nginx

```bash
ansible all -i hosts -m apt -b -a "name=nginx state=latest"
```

> Nb : use '-b' argument to make vagrant user use sudo (required)

> Nb : we use apt for debian, use yum package manager for centos

Use module service to restart nginx

```bash
ansible all -i hosts -m service -b -a "name=nginx state=restarted"
```

### Define ansible playbook to install Nginx


Define hosts 

```yaml
---
  - hosts: all
```
> '---' is not mandatory but it designated Yaml file to interpretor

Need Sudo 

```yaml
---
  - hosts: all
    become: true
```

Add task to ensure nginx service is installed

```yaml
---
  - hosts: all
    become: true
    tasks:
    -
      name: "Ensure nginx is at the latest version"
      apt: "name=nginx state=latest"

```


Add task to ensure nginx is started

```yaml
---
  - hosts: all
    become: true
    tasks:
    -
      name: "Ensure nginx is at the latest version"
      apt: "name=nginx state=latest"
    -
      name: "start nginx"
      service:
        name: nginx
        state: started

```


### Use Ansible playbook with Vagrant provider


Use 'ansible_local' provisioning

```ruby
config.vm.provision "ansible_local" do |ans|
    ans.playbook = "playbook.yml"
    ans.install = true
    ans.install_mode = "pip"
end
```

> Nb: use playbook path on remote host ! (ex /home/vagrant/playbook.yml)

Reload VM with new provision

```bash
vagrant provision
```

### Use Ansible playbook in standalone

Replace shell script to use ansible provision :

```bash
ansible-playbook -i hosts playbook.yml
```

> Nb: we do not define host 'all'


## Playbooks useful tips


### Use loop

Example for creating user ssh

```yaml
## users.yml
---
- hosts: "localhost"
  connection: "local"
  vars:
    users:
    - "paul"
    - "tanya"
    - "ruby"
  tasks:
  - name: "Create user accounts"
    user:
      name: "{{ item }}"
      groups: "admin,www-data"
      state: "present"
    with_items: "{{ users }}"
  - name: "Add authorized keys"
    authorized_key:
      user: "{{ item }}"
      key: "{{ lookup('file', 'files/'+ item + '.key.pub') }}"
    with_items: "{{ users }}"
```

Store ssh pub keys in files directory 

```bash
├── files
│   ├── paul.key.pub
│   ├── ruby.key.pub
│   └── tanya.key.pub
├── users.yml
```

Change sudoer list with regex

```yaml
- name: "Allow admin users to sudo without a password"
    lineinfile:
      dest: "/etc/sudoers"
      state: "present"
      regexp: "^%admin"
      line: "%admin ALL=(ALL) NOPASSWD: ALL"
```


### Store task result

Example : Use URI module and debug variable

```yaml
- name: Get page content
  uri: 
    url: "http://169.254.169.254/info"
    return_content: yes
  register: page_result

- name: Show page_result
  debug: var=page_result

```

### Execute shell script

Example to run docker cmd

```yaml
- name: Run Container
  shell: "docker run -p 80:5000 -d playground:pgflask"
  args:
   chdir: "/tmp"
  become: yes
```

> nb: you assure docker cli is installed before

### Task executed only when condition

Download `docker-compose` script only if not exists 

```yaml
- name: Check if docker-compose file exists
  stat: 
    path: /usr/local/bin/docker-compose
  register: docker_compose

- name: Install Docker-compose
  shell: curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose"
  become: true
  when: docker_compose.stat.exists == False

```

you can cast variable 

```yaml
when: whatever_var|bool
```

## Exercice

### Configure an Nginx HTTP Server 

* Start a VM with static IP 192.169.10.99
* use ansible to configure VM with Nginx server
* Serve local HTML files with VM server

#### How to check ?

You should see your html page content with this command

```bash
curl 192.169.10.99
```

