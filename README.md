ansible_role_client
=========


[![CI](https://github.com/habbis/ansible_role_client/workflows/CI/badge.svg)](https://github.com/habbis/ansible_role_client/actions?query=workflow%3ACI)

This role setup wireguard client on a linux machine.

FYI wireguard dont really have client its calle peers since i am going to make 
a playbook to manage peer on a wireguard server it better to call this clients.

It will create a wireguard client for so you can access your network via your
server. 

Example client config 

```
[Interface]
PrivateKey = your-client-privatekey
# the client wireguard ip address you want to give it.
Address = 11.46.1.1/32
# you can supply dns server to client
DNS = yourdns, yourdns

[Peer]
PublicKey = server-public-key
# if set 0.0.0.0/0 the your client will send all network traffic
# via the wireguard server.
AllowedIPs = 0.0.0.0/0
Endpoint = your-wireguard-server-ip:51820
PersistentKeepalive = 21

```

Example site.yml

```
---
- name: setup puppet agent
  gather_facts: yes
  remote_user: root
  #remote_user: ansible
  #become: yes
  #become_method: sudo
  hosts: test
  #hosts: puppet-clients
  #hosts: all
  vars_files:
    -  defaults/main.yml
    #-  defaults/secrets.yml

  roles:
    - { role: ../ansible_role_client_server }
```
