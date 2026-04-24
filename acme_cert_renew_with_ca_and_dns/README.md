# README

## Overview

This project is for demonstrating how to issue and renew cert automatically when you have controll to your CA and DNS.

## Architecture

You will need to prepare below things:

- A host to run ansible playbook, with below collections, in this demo, I use localhost as the controller. (controller)
  - community.general
  - community.crypto
  - connection to all hosts listed below
- DNS server that can be controlled by `community.general.nsupdate` module. (dns)
- CA server that supports `dns-01` challenge method. (ca)
- Nginx server, as a reverse proxy to other services, certs will be deployed to here. (nginx)

## Design of playbook and roles

The playbook will call 3 roles with variables provided by `group_vars/acme`.

roles:
    - `dns_config`
    - `cert_renew`
    - `nginx_config`

First, `dns_config` will add dns records of services. After that, `cert_renew` will use key and cert request file on the ca host to request a new cert through DNS challege, a text record will be added to DNS server, once the chanllege is passed, the new cert will be generated. Finally the generated certs and private keys will be copied to nginx host, and update the nginx config through `nginx_config` role.

**In common practice the private key should be kept by owner but not the ca, owner will only submit the cert request to the ca. Since this is project is for demonstration so I didn't create another host to generate cert request.**
