# MyAnsibleTemplate

## Check List
* target hosts in /site.yml
* target IP in Inventory files,　/developer or /production
* vars else in Var files, /vars/all, /vars/development or /vars/production
* secret vars (like password) in Var files, /secrets.yml
  - how to edit
    1. $ ansible-vault decrypt secrets.yml
    1. $ vi secrets.yml
    1. $ ansible-vault encrypt secrets.yml


* `$ ansible-playbook -i development site.yml -u DEPLOY_USER -K -vvvv --ask-vault-pass`
