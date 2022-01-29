# eth-validator-node
Ansible setup for Ubuntu server ETH validator node.

## Update inventory

    [ethereum]
    192.168.1.100 ansible_user=USERNAME ansible_port=PORT

## Download dependencies

    ansible-galaxy install -r requirements.yml 

## Ensure all nodes have SSH access keys uploaded

    https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2

## Run 

    ansible-playbook main.yml --ask-become-pass

## Key Gen

    sudo ./deposit new-mnemonic --num_validators 1 --mnemonic_language=english --chain prater

    sudo scp -r -P 777 validator_keys hackerman@chain:~/validator_keys

    sudo lighthouse --network prater account validator import --directory ~/validator_keys --datadir /var/lib/lighthouse

    sudo chown -R lighthouse:lighthouse /var/lib/lighthouse/prater/validators
    sudo chmod 700 /var/lib/lighthouse/prater/validators

    sudo systemctl restart lighthouse-validator

## Todo
1. Connect to personal hotspot in emergency
2. VPN for webs
3. dyndns
4. Protect Grafana and Prometheus webs
5. when to update node?
6. Alerts
7. Logs
8. Grafana password
9. Auto add Lighthouse dashboard
10. Auto add Geth dashboard


https://www.coincashew.com/coins/overview-ada/guide-how-to-build-a-haskell-stakepool-node/how-to-harden-ubuntu-server

https://someresat.medium.com/guide-to-staking-on-ethereum-2-0-ubuntu-lighthouse-41de20513b12
https://someresat.medium.com/guide-to-staking-on-ethereum-2-0-ubuntu-prater-lighthouse-794d3cd7cf4e