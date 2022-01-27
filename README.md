# eth-validator-node
Ansible setup for Ubuntu server ETH validator node.

## Update inventory

    [ethereum]
    192.168.1.100 ansible_user=USERNAME ansible_port=PORT

## Download dependencies

    ansible-galaxy install -r requirements.yml 

## Run 

    ansible-playbook BOOK.yml --ask-become-pass
