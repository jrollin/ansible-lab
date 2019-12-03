# Ansible CI

* Install gitlabce with ansible galaxy
* scm git to build code
* deploy with ansible if build ok


Use galaxy module : https://galaxy.ansible.com/geerlingguy/gitlab


Install inline :

```bash
ansible-galaxy install geerlingguy.gitlab
```
or 

Install role from file : 

```bash
ansible-galaxy install -r requirements.yml
```

Check everythin ok 

```bash
ansible gitlab -i inventory -m ping

```

## playbook gitlab

```bash
ansible-playbook -i inventory gitlabce.yml
```

When succeed

* Access gilab on VM HOST 
* change password with 'adminadmin'
* acess admin with 'root' and previous password