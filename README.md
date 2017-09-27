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

## Files
* Playbook file : site.yml
* Inventory files : deployment / production
* Config file : ansible.cfg

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
