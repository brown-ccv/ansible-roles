# Ansible Roles

This repository contains a collection of ansible roles common to our CCV
deployments. They cover common infrastructure pieces like `docker` and `nginx`.

## Install

To install this collection. Add a `requirements.yml` file in the root of your
ansible project.

```
---
collections:
- name: https://github.com/brown-ccv/ansible-roles/releases/download/0.0.5/ccv-ansible-0.0.5.tar.gz
  version: 0.0.5
```

The name should be the URL of the latest release of the `brown-ccv/ansible-roles`
repository. There's an asset for each release made that's named
`ccv-ansible-${VERSION}.tar.gz`, use that. The other stuff are tarballs of the
source, and they don't install correctly via `ansible-galaxy`.

To install this collection run:

```
ansible-galaxy collection install -r requirements.yml
```

That will install the roles under `~/.ansible/collections/`. To install a new
version, update the `name` and `version` fields in the `requirements.yml` file
and run:

```
ansible-galaxy collection install -r requirements.yml  --force-with-deps
```

The `--force-with-deps` forces a reinstall.

## Usage

Add the following stanza to the top of your `site.yml` file under `hosts`.

```
collections:
- ccv.ansible
```

This will pull in the `ccv.ansible` collection and give the playbook access to
any of the roles within `ccv.ansible`. To use a role from the collection
reference it like you would any other role. For example:

```
  - role: docker
    become: yes
    tags:
    - docker
```

### nginx

The nginx role has some options that need to be passed. If you're using Let's
Encrypt for your site you need:

```
- role: nginx
  become: yes
  vars:
    letsencrypt: true
```

That signals the role to install the Let's Encrypt nginx configuration.

To use a static SSL certificate you need the following variables defined.

```
- role: nginx
  become: yes
  vars:
    static_ssl: true
    ssl_certificate: your_solo_certificate.cer
    ssl_ca_certificate: your_ca_certificate_chain.cer
    ssl_private_key: your_private_key.key
```

Where `your_solo_certificate.cer` is the name of the file containing your
site's certificate (without the chain). `your_ca_certificate_chain.cer` is the
certificate chain (root and intermediates) without your site's certificate.
`your_private_key.key` is the private key used to generate your certificate.

Place those files in a `files` directory in the root of your ansible project.
Make sure and encrypt the key file with `ansible-vault` before committing
anything.
