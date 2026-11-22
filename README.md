mebooks.ansible-role-docker-tomcat
==================================

Runs EPUBCheck as a web service: a Tomcat container from an image built with
[epubcheck-web](https://github.com/jcdarwin/epubcheck-web), routed by Traefik at `epubcheck.<domain>`.

We presume the use of [Traefik](traefik.io) as our reverse proxy,.

We assume that we've probably run:

* [ansible-role-users](https://github.com/jcdarwin/ansible-role-users)
* [ansible-role-common](https://github.com/jcdarwin/ansible-role-common)
* [ansible-role-docker](https://github.com/jcdarwin/ansible-role-docker)

The above all chain as dependencies of our main dependency:

* [ansible-role-docker-traefik](https://github.com/jcdarwin/ansible-role-docker-traefik)

The image contains the webapp, so nothing is mounted from the host. The image isn't on a registry:
build it, then load it on the host before running the role:

    docker save mebooks/epubcheck:5.4.0-1 | gzip | ssh deploy@host 'gunzip | sudo docker load'

The container's log is rotated (`max-size: 10m`, `max-file: 5`). Tomcat logs only to the console.

Requirements
------------

Developed and tested on *Ubuntu Server 16.10 Yakkety*, but should work on other OSes.

It's running on Docker.

Role Variables
--------------

- `tomcat.domain` defaults to *domain.tld*
- `tomcat.source`: defaults to *domain.tld*
- `tomcat.image`: the image to run; defaults to *epubcheck-web:latest*. Pin a tag, e.g. `mebooks/epubcheck:5.4.0-1`

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
      image: mebooks/epubcheck:5.4.0-1

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
