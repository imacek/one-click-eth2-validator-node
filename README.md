# eth-validator-node
Ansible setup for Ubuntu server ETH validator node.

## Update inventory

    [ethereum]
    192.168.1.100 ansible_user=USERNAME ansible_port=PORT
    [loki]
    192.168.1.100
    [promtail]
    192.168.1.100

## Update group_vars/all 

- Whatever you don't like in defaults.
- Default networks are testnets - change to mainnets if production node.
- Highly engouraged to change ssh port and disable password auth.
- Lighthouse monitoring endpoint can be set to beaconcha.in: https://kb.beaconcha.in/beaconcha.in-explorer/mobile-app-less-than-greater-than-beacon-node.

## Download dependencies

    ansible-galaxy install -r requirements.yml 

## Ensure all nodes have SSH access keys uploaded

    https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2

## Run 

    ansible-playbook main.yml --ask-become-pass

## Generate validator keys

    sudo ./deposit new-mnemonic --num_validators 1 --mnemonic_language=english --chain prater

    sudo scp -r -P 777 validator_keys USER@HOST:~/validator_keys

    ssh USER@HOST

    sudo lighthouse --network prater account validator import --directory ~/validator_keys --datadir /var/lib/lighthouse

    sudo chown -R lighthouse:lighthouse /var/lib/lighthouse/prater/validators
    sudo chmod 700 /var/lib/lighthouse/prater/validators

    sudo systemctl restart lighthouse-validator

## Manual Steps

1. Import Grafana dashboards from local "grafana/" directory. More exist here: https://github.com/sigp/lighthouse-metrics/tree/master/dashboards

## Todo

5. when to update node? unattended-upgrades send email https://github.com/jnv/ansible-role-unattended-upgrades
12. Disaster scenario - plan for backup
    15. test full build once
14. Wireguard vpn https://github.com/githubixx/ansible-role-wireguard

## References
- https://someresat.medium.com/guide-to-staking-on-ethereum-2-0-ubuntu-lighthouse-41de20513b12
- https://someresat.medium.com/guide-to-staking-on-ethereum-2-0-ubuntu-prater-lighthouse-794d3cd7cf4e
- https://www.coincashew.com/coins/overview-eth/guide-how-to-stake-on-eth2-with-lighthouse
- https://www.coincashew.com/coins/overview-ada/guide-how-to-build-a-haskell-stakepool-node/how-to-harden-ubuntu-server
- https://www.coincashew.com/coins/overview-ada/guide-how-to-build-a-haskell-stakepool-node/how-to-setup-wireguard
