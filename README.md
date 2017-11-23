### Roles: ansible-user
- Playbook para adicionar e gerenciar usuarios no Linux

## Sistema Operacional
- Familia Red Hat

## Pre-requesitos
- Gerando senha criptografada

```bash
$ openssl passwd -1
```

- Gerando chaves ssh-key por usuario

```
$ ssh-keygen -t rsa -b 2048 -v -f keyname
```

## Inventario
- Arquivo de inventario para adicionar novos hosts

```bash
[linux-hyperv]
localhost
```
## Playbook: deploy-user.yml
```yml
---
- hosts: linux-servers
  become: true
  become_user: root
  gather_facts: false
  tasks:
    - name: Criando usuario remoto
      user:
       name: remoto
       comment: Usuario padrao de acesso remoto SSH
       groups: wheel
       shell: /bin/bash
       password: $1$2cn8rY.D$VBMDKK6JqViPMyjUNPIib/
      tags: user

    - name: Setando chave ssh key
      authorized_key:
       user: remoto
       state: present
       key: "{{ lookup('file', 'remoto.pub') }}"
      tags: ssh-key

    - name: Copiando file sudoers
      copy:
       src: sudoers
       dest: /etc/sudoers
       owner: root
       group: root
       mode: 0444
      tags: sudo

    - name: Padrinozando senha de root
      user:
       name: root
       password: $1$2cn8rY.D$VBMDKK6JqViPMyjUNPIib/
      tags: user

    - name: Setando chaves ssh key
      authorized_key:
       user: root
       state: present
       key: "{{ lookup('file', 'ansible.pub') }}"
      tags: ssh-key
```

## Executando playbook:
- O playbook é executado e agendado pelo propio gitlab em modo de ci/cd.

## Autor:
- [Fabio Coelho](http://gitlab.braspress.com.br/fabiocoelho-sao)
