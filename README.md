# MyAnsibleTemplate
----

## Preparation
* make the account for exec ssh
`$ useradd deploy_user`
`$ usermod -G wheel deploy_user`
`$ passwd deploy_user`

* if use PasswordAuthentication
- modify target host's`/etc/ssh/sshd_config` => `PasswordAuthentication no` -> `PasswordAuthentication yes`
- add `ansible.cfg` => `[defaults] ask_pass = True`

* If you use AWS as infrastructure, you need plus
`$ sudo cp -arp /home/<ec2-user or centos>/.ssh /home/deploy_user/`
`$ sudo chown -R deploy_user /home/deploy_user/.ssh`
`$ sudo visudo -f /etc/sudoers.d/cloud-init`
`deploy_user ALL = (ALL) ALL  #<- 追記`

* if use PortForwarding
- modify target host's`/etc/ssh/sshd_config` => `#AllowAgentForwarding yes` -> `AllowAgentForwarding yes`


* Check this vars for your project
`vars/all.yml`

## Files
* Playbook file : site.yml
* Inventory files : deployment / production
* Config file : ansible.cfg
* under tasks always read the vars file, `vars/all.yml`
  - common
  - git
  - ruby
  - postgres

## Check List
* target hosts in /site.yml
* target IP in Inventory files,　/developer or /production
* vars else in Var files, /vars/all, /vars/development or /vars/production
* secret vars (like password) in Var files, /secrets.yml
  - how to edit
    1. $ ansible-vault decrypt secrets.yml
    1. $ vi secrets.yml
    1. $ ansible-vault encrypt secrets.yml

## How to use
* `$ ansible-playbook site.yml -i development -vvvv`
* `$ ansible-playbook site.yml -i development -K -vvvv --ask-vault-pass`


## Config
* 対象ホストへのSSHユーザは`deploy_user`としています. 対象ホストにこのアカウントを作成してください.
* SSH秘密鍵名は`id_rsa`としています. 実行ホストに`$HOME/.ssh/id_rsa`, 対象ホストに`/home/deploy_user/.ssh/id_rsa.pub`を用意してください.
※ ansible.cfgにて設定


## About role "act-****"
* 冪等性を保証しない作業を複数台に実施する際に利用するRole.
* 基本的に `hosts: local_action` と組み合わせて使うことを前提に作成されている.
* 冪等性を保証しないため, 不用意に複数回実施すると想定外の結果を返す可能性がある. 取り扱いに気をつけること.
* 一括作業対象ノードには同一ユーザ, 同一パスワードでログインできることが実行の前提となる. （ユーザはansible.cfgで指定しているもの）

```example of site.yml
- name: Exec Action role
  hosts: local_action
  gather_facts: no
  sudo: yes
  roles:
    - act-reachability
    - act-debugcmd
```

## Appendix
1. Select the inventory file with option `-i`
`$ ansible -i <Inventory file name> <hosts or groups name>`
    ex.
      `$ ansible -i development all -m ping`
      `$ ansible-playbook site.yml -i production`


1. 対象ホストに指定ユーザを作成
  - 設定したいパスワードのハッシュを作成
  - ansibleでユーザ作成
  `$ ansible development web -m user -a "name=tomcat_user group=wheel password=xxxxxxxxxx shell=/bin/zsh createhome=no"`
  `$ ansible development hosts -m user -a "name=testuser group=wheel password=xxxxxxxxxx shell=/bin/bash createhome=yes"`
