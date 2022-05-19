# polkadot-ansible
Ansible scripts to deploy a Docker based Polkadot/Kusama validator node. No need for compiling and complicated dependency management :)

## Getting started

Prerequisits:

*  VPS or dedicated server meeting the Polkadot/Kusama requirements (you might want to have a beefier machine as we're going to run two validators on one machine)
*  ansible (2.11.6+)
*  ansible-playbook (2.11.6+)
*  new user with guid and uid 1000 free to be created on the host system (currently using hardcoded docker bind mount instead volumes)

First copy the `hosts.ini-sample` to `hosts.ini` then:

1.  Rename the two `my_node_*` entries to a name you like, also replace the `<IP of your server>` with the actual IP of your server
1.  Copy/Rename `host_vars/my_node_*.yml` files to the same name you just called the host in the `hosts.ini` file.
1.  Update the values in the `host_vars/my_node_*.yml` files, you always want to:
  1.  Update the `node_name` this is the node name your validator will show up with in the Telemetry dashboards
  1.  Update the `polkadot_db_snapshot_url` and `polkadot_db_snapshot_checksum` with the latest values from https://polkashots.io/

## Deployment

If you feel adventerous you can deploy the whole server using:

```
$ ansible-playbook -i hosts.ini all.yml
```

This will execute the following roles:

* polkadot-setup
  *  Setup Docker
  *  Setup Journald
  *  Setup motd (message of the day)
* polkadot-restore-db
  *  Downloads the in the host_vars defined snapshot and unpacks it to the future `data_path`
* polkadot-validator
  *  Starts the polkadot/kusama validators as docker containers
* polkadot-rotate-keys
  *  Rotates the session keys so you can use for the `SetSessionKeys` extrinsic

You can also run the individual roles using the `setup_*.yml` playbooks instead of `all.yml`.

### Polkadot upgrades

To upgrade to the latest Polkadot version you can simply restart the containers using the `polkadot-validator` playbook.

## Notes

Monitoring is not yet covered in these ansible playbooks. Do NOT run a Polkadot/Kusama validator w/o proper monitoring or you will get slashed.

You should not use `root` user on the server, instead replace the `ansible_user` field in `hosts.ini` with an unpriviledged user (which has docker rights).
