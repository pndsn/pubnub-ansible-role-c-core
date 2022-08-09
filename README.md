# PubNub Ansible Role C Core

Clone PubNub C Core Repository and it's dependencies in MacOS

## Pre-requisites

Install the following packages in your MacOS:

- ansible
- git

## TLDR;

Locally install, setup, and run a sample PubNub C demo using Ansible.

```
ansible-playbook pubnub_c_core_mgmt.yml -i inventory -e "@vars.yml" -t "deploy,sample"
```

## Ansible Inventory

    > This is normal Ansible language/configuration from now on

Create your inventory file with your credentials for local or remote installation if not already.

`inventory`:
```
[local]
localhost ansible_become_password=CHANGEME ansible_user=USER
[iot]
raspberrypi ansible_become_password=CHANGEME ansible_user=RASPI
raspberrypi2 ansible_become_password=CHANGEME ansible_user=RASPI
```

## (Optional) Override Vars

Do not change `openssl_install_dest` unless you modify your `c_core_dest/openssl/posix.mk`. See more at the bottom.

| VAR                  | EFFECT                   | REQUIRED | DEFAULT                |
|----------------------|--------------------------|----------|------------------------|
| global_hosts         | hosts                    | true     | local                  |
| c_core_dest          | PubNub C Core location   | false    | ansible_env.HOME       |
| openssl_temp_dest    | Openssl build location   | false    | /tmp/openssl           |
| openssl_install_dest | PubNub Openssl link file | false    | /usr/local/opt/openssl |

## Run with Ansible Tags

This is mostly done when you already have a something installed and you want to only run a specific task because something changed somewhere.

```
ansible-playbook pubnub_c_core_mgmt.yml -i inventory -e "@vars.yml" -t "<TAG1>,<TAG2>"
```

### Install Dependencies for MacOS [deploy]

```
ansible-playbook pubnub_c_core_mgmt.yml -i inventory -e "@vars.yml" -t "<TAG1>,<TAG2>"
```

### Reference Tags

You can "manually" install all or specific programs use tags:

| TAG             | EFFECT                     |
|-----------------|----------------------------|
| deploy          | Runs all tags here         |
|||
| install         | install all                |
| install-mac     | install MacOS dependencies |
| install-openssl | install openssl for MacOS  |
| install-pubnub  | install PubNub C Core      |
| sample          | run sample PubNub Program  |

To uninstall all or specific programs use tags:

| TAG               | EFFECT                                |
|-------------------|---------------------------------------|
| undeploy          | runs all tags here                    |
|||
| uninstall-openssl | uninstall openssl for MacOS [limited] |


## Notes

### Change the default openssl library needed by PubNub

I don't recommend going this way because there might be more places where changing the `$(LDLIBS)` variable might have averse effects in the required libs. Also this is only tested using MacOS M1 with architecture ARM64 which is compilled by default when ran with --tags="deploy,install-openssl" but it might work with other MacOS architectures not running Apple M1 Chip; ansible should automatically detect your architecture and attempt to install `openssl` with the correct `./Configuration` flags (See more on task `c-core-role/tasks/installed_pubnub.yml` line 108: `LDLIBS = -lpthread -lssl -lcrypto -L/usr/local/opt/openssl/lib`) 

### Testing

Currently there are no tests. Using `molecule` is a common practice. I don't know about testing OSX, maybe AWS VMs or physical machines.