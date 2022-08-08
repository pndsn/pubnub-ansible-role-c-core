# PubNub Ansible Role C Core

Clone PubNub C Core Repository and it's dependencies in MacOS

## Pre-requisites

Install the following packages in your MacOS:

- homebrew
- ansible


## Install

```
ansible-playbook pubnub_c_core_mgmt.yml -i inventory -e "@vars.yml" -t "install"
```
