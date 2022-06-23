# One Click ETH2 Validator Node
Ansible setup for Ubuntu server ETH2 validator node which includes:
- Lighthouse 
- Geth
- Prometheus (with PagerDuty alerting)
- Grafana
- SSH Hardening
- Wireguard VPN

## Before you start

Go to `group_vars/all` and alter to your liking. Defaults point to testnets.

## Update inventory in /etc/ansible/hosts

    [ethereum]
    192.168.1.100 ansible_user=USERNAME ansible_port=PORT

## Download Ansible dependencies

    ansible-galaxy install -r requirements.yml 

## Run 

    ansible-playbook main.yml --ask-become-pass

## Generate and import validator keys

    sudo ./deposit new-mnemonic --num_validators 1 --mnemonic_language=english --chain prater
    sudo scp -r -P 22 validator_keys USER@HOST:~/validator_keys

    ssh USER@HOST

    sudo lighthouse --network prater account validator import --directory ~/validator_keys --datadir /var/lib/lighthouse/prater

    sudo chown -R lighthouse:lighthouse /var/lib/lighthouse/prater/validators
    sudo chmod 700 /var/lib/lighthouse/prater/validators

    sudo systemctl restart lighthouse-validator

## Manual Steps

1. Import Lighthouse Grafana dashboards from https://github.com/sigp/lighthouse-metrics/tree/master/dashboards

# Keep system up to date

## Update Linux packages (including latest Geth version)

    sudo apt update
    sudo apt upgrade -y
    sudo apt autoclean

## Restart the system - betwen validator attestations

    sudo reboot

## Check Geth and Lighthouse are running

    journalctl -fu geth
    journalctl -fu lighthouse-beacon        # Look for "INFO Previous epoch attestation(s) success" line or check beaconcha.in
    journalctl -fu lighthouse-validator

## Upgrade Lighthouse - betwen validator attestations

Go to `group_vars/all` and update `lighthouse_version` to latest that can be found https://github.com/sigp/lighthouse/releases.
Then run:

    ansible-playbook lighthouse.yml --ask-become-pass

## Check Lighthouse is running

    journalctl -fu lighthouse-beacon        # Look for "INFO Previous epoch attestation(s) success" line or check beaconcha.in
    journalctl -fu lighthouse-validator

## References
- Lighthouse book: https://lighthouse-book.sigmaprime.io/intro.html
- Lighthouse Mainnet setup: https://someresat.medium.com/guide-to-staking-on-ethereum-2-0-ubuntu-lighthouse-41de20513b12
- Lighthouse Testnet setup: https://someresat.medium.com/guide-to-staking-on-ethereum-2-0-ubuntu-prater-lighthouse-794d3cd7cf4e
- More Lighthouse setup guides: https://www.coincashew.com/coins/overview-eth/guide-how-to-stake-on-eth2-with-lighthouse
- How to prune Geth DB: https://gist.github.com/yorickdowne/3323759b4cbf2022e191ab058a4276b2
- Wireguard VPN server setup: https://www.smarthomebeginner.com/linux-wireguard-vpn-server-setup/
- SSH Key management: https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2
- Ubuntu hardening: https://www.coincashew.com/coins/overview-ada/guide-how-to-build-a-haskell-stakepool-node/how-to-harden-ubuntu-server
- Extend LVM partitions: https://packetpushers.net/ubuntu-extend-your-default-lvm-space/
