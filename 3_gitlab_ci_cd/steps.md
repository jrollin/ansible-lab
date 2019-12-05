# Gitlab + CI + CD with Ansible

@todo : Vagrant + ansible

## What you will learn

@todo

## What you will build

@todo : schema

## What you Need

@todo




* Install gitlabce with ansible galaxy
* scm git to build code
* deploy with ansible if build ok


Use galaxy module : https://galaxy.ansible.com/geerlingguy/gitlab


Install inline :

```bash
ansible-galaxy install geerlingguy.gitlab
```
or (recommanded)

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
* access admin with 'root' and previous password



## Ajouter des runners

Go to URL '/admin/runners' to retrieve runner token


```bash
ansible-playbook -i inventory gitlab-runner.yml
```

Check everything ok

```bash
ansible -i inventory gitlab-runner -m ping 
```

```bash
ansible -i inventory gitlab-runner  gitlab-runner.yml --extra-var gitlab_registration_token='TOKEN'
```



## run project ci tests

gitlabci + docker runner

## deploy on success


```yaml
deploy_app:
  stage: deploy
  tags:
    - ansible
  script:
    - 'ansible-playbook -i staging deploy.yml'
```


```bash
- hosts: gitlab_runner
  …
  - tasks:
   - name: create ssh key if it does not exist
      expect:
        command: ssh-keygen -t rsa
        # only creates the key if the file does not exist
        creates: "{{ runner_user_home }}/.ssh/id_rsa"
        ...
        responses:
          "file": "{{ runner_user_home }}/.ssh/id_rsa"
          "passphrase": ""
   - name: read public key
      command: "cat {{ runner_user_home }}/.ssh/id_rsa.pub"
      register: runner_pub_key


- hosts: deploy_target
  …
  - tasks:
   - name: add deploy key to authorized keys
      authorized_key:
        user: "{{ user }}"
        key: "{{ hostvars[ deploy_source_host ].runner_pub_key.stdout}}"

```