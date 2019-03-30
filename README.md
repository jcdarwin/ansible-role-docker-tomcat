mebooks.ansible-role-docker-tomcat
==================================

Installs Tomcat using the [tomcat:alpine docker image](https://hub.docker.com/_/tomcat/).

We presume the use of [Traefik](traefik.io) as our reverse proxy, and use git to back our wiki content up to a repository.

We assume that we've probably run:

* [ansible-role-users](https://github.com/jcdarwin/ansible-role-users)
* [ansible-role-common](https://github.com/jcdarwin/ansible-role-common)
* [ansible-role-docker](https://github.com/jcdarwin/ansible-role-docker)

The above all chain as dependencies of our main dependency:

* [ansible-role-docker-traefik](https://github.com/jcdarwin/ansible-role-docker-traefik)

We mount the webapps content from: `/etc/tomcat/webapps`

Requirements
------------

Developed and tested on *Ubuntu Server 16.10 Yakkety*, but should work on other OSes.

It's running on Docker.

Role Variables
--------------

- `tomcat.domain` defaults to *domain.tld*
- `tomcat.source`: defaults to *domain.tld*

Dependencies
------------

- `mebooks.ansible-role-docker-traefik`

Example Playbook
----------------

```yml
- hosts: remote
  remote_user: deploy
  become: true
  become_user: root
  become_method: sudo

  vars:
    traefik:
      traefik_testing: true
      owner: administrator
      domain: mebooks.co.nz
      email: mebooks.support@gmail.com
      users:
      # owner_password / owner_password_encrypted are defined in the unversioned group_vars/remote
      - username: "{{ owner }}"
        password: "{{ owner_password }}"
        acl:
        - traefik

    tomcat:
      domain: mebooks.co.nz
      source: mebooks.co.nz

  roles:
    # We presume we've already run ansible-role-users and ansible-role-common
    # The following role is listed as dependencies in ansible-role-docker-traefik/meta/main.yml:
    # - role: ansible-role-docker
    # The following role is listed as dependencies in ansible-role-docker-ghost/meta/main.yml:
    # - role: ansible-role-docker-traefik
    - role: ansible-role-docker-tomcat
```

License
-------

GPLv3

Author Information
------------------

Jason Darwin
