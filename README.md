Wekan [![Ansible Galaxy](https://img.shields.io/badge/galaxy-Wekan.Server-blue.svg?style=flat)]() [![Testing](https://img.shields.io/badge/status-testing-orange.svg?style=flat)]()
===========
Deploy [Wekan](https://wekan.io), the open-source Trello-like kanban, with [Ansible](http://ansible.com)!

Credits
--------
This role is mostly based on the [Rocket.Chat Ansible role](https://github.com/RocketChat/Rocket.Chat.Ansible).

It is a really raw draft and will need improvement before actually working.

Features
--------
- __Optional full stack deployment:__  
    Fully deploy [Wekan](https://wekan.io), including [MongoDB](http://mongodb.com) & an [Nginx](https://www.nginx.com/) reverse SSL proxy.  
    Or, deploy [Wekan](https://wekan.io) and integrate with your existing [MongoDB](http://mongodb.com) and/or [Nginx](https://www.nginx.com/) instances/deployment methods.  

- __Optional automatic SSL cert generation:__  
    Automatically generate (self signed) SSL certs for HTTPS connectivity via an [Nginx](https://www.nginx.com/) reverse proxy.  
    Or, deploy your own SSL certs!  

- __Optional automatic upgrades [requires Ansible 2.0]:__  
    If a new version of [Wekan](https://wekan.io) is released, or if you want to follow development for testing purposes, simply update the `wekan_version` to whichever release you wish to deploy (see [the Wekan releases page](https://github.com/wekan/wekan/releases), set `wekan_automatic_upgrades` to `true` and `wekan_version` to the latest version and let this role do the rest!
    If there's a change to the code deployed to your [Wekan](https://wekan.io) server (either because of a remote change to the `wekan_version` you're following, 'latest' or 'develop' for instance, or because you set a new `wekan_version` to fetch), this role will handle the upgrade and redeployment of the [Wekan](https://wekan.io) service, keeping your data in tact.  
	_Note: This functionality requires Ansible 2.0. See how to fetch the 2.0 version of this role in the [Install from Ansible Galaxy secion](#install-the-ansible-20-version-of-this-role)_

Supported Platforms
-------------------
### Debian
- Jessie (8)

### Ubuntu
- Trusty: 14.04
- Vivid: 15.04

### EL (RHEL/CentOS)
- 7

Support for other distributions/operating systems is planned for the near future.
If you'd like to see your distribution/operating system supported, please [raise an issue](https://github.com/BackIsBachus/wekan.ansible/issue)!

Running into problems?
----------------------
Please be sure you've read the [FAQ](#faq) and all documentation before raising an issue.  

Role Variables
--------------
All variables have sane defaults set in [`defaults/main.yml`](defaults/main.yml)
### Defaults

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `wekan_automatic_upgrades` | false | A boolean value that determines whether or not to upgrade Wekan upon source code changes |
| `wekan_upgrade_backup` | true | A boolean value that determines whether or not to back up the current Wekan version when upgrading |
| `wekan_upgrade_backup_path` | `"{{ wekan_application_path }}"`| The path to store the back up of Wekan when `wekan_upgrade_backup` is `true` |
| `wekan_application_path` | `/var/lib/wekan` | The destination on the filesystem to deploy Wekan to |
| `wekan_version` | `latest` | The version of Wekan to deploy; see the [Wekan releases page](https://github.com/wekan/wekan/releases) for available options |
| `wekan_tarball_remote` | See [`defaults/main.yml`](defaults/main.yml) | The remote URL to fetch the Wekan tarball from (uses `wekan_version`) |
| `wekan_tarball_sha256sum` | See [`defaults/main.yml`](defaults/main.yml) | The SHA256 hash sum of the Wekan tarball being fetched |
| `wekan_tarball_fetch_timeout` | 100 | The time (in seconds) before the attempt to fetch the Wekan tarball fails |
| `wekan_tarball_validate_remote_cert` | true | A boolean value that determines wether or not to validate the SSL certs for the Wekan tarball remote |
| `wekan_service_user` | `wekan` | The name of the user that will run the Wekan server process |
| `wekan_service_group` | `wekan` | The name of the primary group for the `wekan_service_user` user |
| `wekan_service_host` | `"{{ ansible_fqdn }}"` | The FQDN of the Wekan system |
| `wekan_service_port` | 3000 | The TCP port Wekan listens on |
| `wekan_node_10_40_path` | `/usr/local/n/versions/node/0.10.40/bin` | The path to the `node` binary directory that n installs |
| `wekan_original_npm` | `/usr/bin/npm` | The path to the original `npm` binary, before n installs any Node versions |
| `wekan_include_mongodb` | true | A boolean value that determines whether or not to deploy MongoDB |
| `wekan_mongodb_keyserver` | keyserver.ubuntu.com | The GPG key server to use when importing the MongoDB repo key |
| `wekan_mongodb_gpg_key` | `7F0CEB10` | The GPG key fingerprint to import for the MongoDB repo |
| `wekan_mongodb_server` | 127.0.0.1 | The IP/FQDN of the MongoDB host |
| `wekan_mongodb_port` | 27017 | The TCP port to contact the MongoDB host host via |
| `wekan_mongodb_packages` | `mongodb` | The name of the MongoDB package(s) to install (differs for different distros - see `vars/`) |
| `wekan_mongodb_config_template` | [`mongod.conf.j2`](templates/mongod.conf.j2) | The `/etc/mongod.conf` template to deploy |
| `wekan_include_nginx`| true | A boolean value that determines whether or not to deploy Nginx |
| `wekan_ssl_generate_certs` | true | A boolean value that determines whether or not to generate the Nginx SSL certs |
| `wekan_ssl_key_path` | `/etc/nginx/wekan.key` | The destination path for the Nginx SSL private key |
| `wekan_ssl_cert_path` | `/etc/nginx/wekan.crt` | The destination path for the Nginx SSL certificate |
| `wekan_ssl_deploy_data` | false | A boolean value that determines whether or not to deploy custom SSL data (cert/key files) |
| `wekan_ssl_key_file` | `~` | If not using SSL cert generation, this is the path to the Nginx SSL private key on the Ansible control node, for deployment |
| `wekan_ssl_cert_file` | `~` | If not using SSL cert generation, this is the path to the Nginx SSL cert on the Ansible control node, for deployment |
| `wekan_nginx_enable_pfs` | true | A boolean value that determines whether or not to enable [PFS](http://en.wikipedia.org/wiki/Perfect_forward_secrecy) when deploying Nginx |
| `wekan_nginx_generate_pfs_key` | true | A boolean value that determines whether or not to generate a PFS key file |
| `wekan_nginx_pfs_key_numbits` | 2048 | Numbits to pass to OpenSSL when generating a PFS key file |
| `wekan_nginx_pfs_key_path` | `/etc/nginx/wekan.pem` | The destination path for the Nginx PFS key file |
| `wekan_nginx_pfs_file` | `~` | If not using PFS key generation, this is the path to the Nginx PFS key on the Ansible control node, for deployment |


Some variables differ between operating systems/distributions.
These are set in the `vars/` directory, typically in a file named after the distribution.

### RHEL/CentOS variables
Set in [`vars/RedHat.yml`](vars/RedHat.yml)

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `wekan_dep_packages` |   - git              | A list of Wekan dependencies to install |
|                            |   - GraphicsMagick   |                                               |
|                            |   - nodejs           |                                               |
|                            |   - npm              |                                               |
|                            |   - make             |                                               |
| `wekan_mongodb_packages` |   - mongodb        | A list of MongoDB server packages to install |
|                                |   - mongodb-server |                                              |
| `wekan_mongodb_repl_lines` | `'replSet=001-rs'` | The value for the MongoDB replica set |
| `wekan_mongodb_fork` |  `true` | A boolean value that sets whether or not to fork the MongoDB server process |
| `wekan_mongodb_pidfile_path` | `/var/run/mongodb/mongodb.pid` | The path to the pidfile for the MongoDB server process |
| `wekan_mongodb_logpath` | `/var/log/mongodb/mongod.log` | The log file path for the MongoDB server |
| `wekan_mongodb_unixsocketprefix` | `/var/run/mongodb` | The path for the MongoDB UNIX socket prefix |
| `wekan_mongodb_dbpath` | `/var/lib/mongodb` | The path for MongoDB to store its databases |
| `wekan_nginx_process_user` | `nginx` | The user for that will be used to spawn the Nginx server process |

### RHEL/CentOS 7 variables
Set in [`vars/RedHat_7.yml`](vars/RedHat_7.yml)

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `wekan_service_update_command` | `systemctl daemon-reload ; systemctl restart wekan` | The command to use to inform the service management system when a service manifest has changed |
| `wekan_service_template` | | |
| `  src` | `wekan.service.j2` | The source template to deploy for the Wekan service manifest |
| `  dest` | `/usr/lib/systemd/system/wekan.service` | The destination to deploy the Wekan service manifest to |
| `wekan_tarball_validate_remote_cert` | false | A boolean value that determines wether or not to validate the SSL certs for the Wekan tarball remote |

### Debian variables
Set in [`vars/Debian.yml`](vars/Debian.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `wekan_dep_packages` |   - git              | A list of Wekan dependencies to install |
|                            |   - graphicsmagick   |                                               |
|                            |   - nodejs           |                                               |
|                            |   - npm              |                                               |
|                            |   - make             |                                               |
| `wekan_mongodb_packages` | - mongodb-server | A list of MongoDB server packages to install |
|                                | - mongodb-shell  |                                              |
| `wekan_mongodb_repl_lines` | `  replication:`              | The value for the MongoDB replica set |
|                                  | `    replSetName:  "001-rs"`  |                                       |
| `wekan_nginx_process_user` | `www-data` | The user for that will be used to spawn the Nginx server process |

### Debian 8 variables
Set in [`vars/Debian_8.yml`](vars/Debian_8.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `wekan_service_update_command` | `systemctl daemon-reload ; systemctl restart wekan` | The command to use to inform the service management system when a service manifest has changed |
| `wekan_service_template` | | |
| `  src` | `wekan.service.j2` | The source template to deploy for the Wekan service manifest |
| `  dest` | `/etc/systemd/system/wekan.service` | The destination to deploy the Wekan service manifest to |
| `wekan_mongodb_apt_repo` | `deb http://repo.mongodb.org/apt/debian wheezy/mongodb-org/3.0 main` | The APT repository for MongoDB |

### Ubuntu variables
Set in [`vars/Ubuntu.yml`](vars/Ubuntu.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `wekan_dep_packages` |   - git              | A list of Wekan dependencies to install |
|                            |   - graphicsmagick   |                                               |
|                            |   - nodejs           |                                               |
|                            |   - npm              |                                               |
|                            |   - make             |                                               |
| `wekan_mongodb_packages` | - mongodb-server | A list of MongoDB server packages to install |
|                                | - mongodb-shell  |                                              |
| `wekan_mongodb_repl_lines` | `  replication:`              | The value for the MongoDB replica set |
|                                  | `    replSetName:  "001-rs"`  |                                       |
| `wekan_nginx_process_user` | `www-data` | The user for that will be used to spawn the Nginx server process |

### Ubuntu 15 variables
Set in [`vars/Ubuntu_15.yml`](vars/Ubuntu_15.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `wekan_service_update_command` | `systemctl daemon-reload ; systemctl restart wekan` | The command to use to inform the service management system when a service manifest has changed |
| `wekan_service_template` | | |
| `  src` | `wekan.service.j2` | The source template to deploy for the Wekan service manifest |
| `  dest` | `/etc/systemd/system/wekan.service` | The destination to deploy the Wekan service manifest to |
| `wekan_mongodb_apt_repo` | `deb http://repo.mongodb.org/apt/debian wheezy/mongodb-org/3.0 main` | The APT repository for MongoDB |

### Ubuntu 14 variables
Set in [`vars/Ubuntu_14.yml`](vars/Ubuntu_14.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `wekan_service_update_command` | `initctl reload-configuration ; service wekan restart` | The command to use to inform the service management system when a service manifest has changed |
| `wekan_service_template` | | |
| `  src` | `wekan_upstart.j2` | The source template to deploy for the Wekan service manifest |
| `  dest` | `/etc/init/wekan.conf` | The destination to deploy the Wekan service manifest to |
| `wekan_mongodb_apt_repo` | `deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.0 multiverse` | The APT repository for MongoDB |
| `wekan_tarball_validate_remote_cert` | false | A boolean value that determines wether or not to validate the SSL certs for the Wekan tarball remote |


Install this role from Ansible Galaxy
-------------------------------------
This role is available for download from [Ansible Galaxy](http://galaxy.ansible.com).
To install this role, and track it in your Ansible code-base, use something similar to the following in your [`requirements.yml`](http://docs.ansible.com/ansible/galaxy.html#id8):

``` yaml
- src: Wekan.Server
  version: master
  path: roles/external/

```
_Note: you must specify `version` as `master` if you're still using Ansible 1.9.4_

### Install the Ansible 2.0 version of this role
With the release of Ansible 2.0, this role is officially supported with some performance enhancements and extra features (automatic upgrades, for instance).  
To use the Ansible 2.0 version of this role, you can install it using the `ansible-galaxy` command line tool using a `requirements.yml` (both mentioned above) to specify the version you wish to use.  

Here's an example `requirements.yml` file to install via `ansible-galaxy` will fetch the Ansible 2.0 code:
``` yaml
  - src: Wekan.Server
    version: v2.0
    path: roles/external
```

Example Playbook
----------------

A simple playbook to run this role on all `wekan_servers` systems:
``` yaml
  - hosts: wekan_servers
    roles:
     - Wekan.Server
```

A playbook to deploy Wekan to `wekan_servers` but exclude the deployment of MongoDB and use an external instance. Also permit automatic upgrades of Wekan (Ansible 2.0 required for `wekan_automatic_upgrades`! See the [Install from Ansible Galaxy secion](#install-the-ansible-20-version-of-this-role)):
``` yaml
  - hosts: wekan_servers

    vars:
      wekan_automatic_upgrades: true
      wekan_include_mongodb: false
      wekan_mongodb_server: 10.19.3.24

    roles:
      - Wekan.Server
```

FAQ
---
- **When I try to deploy using this role, it fails on the `Fetch the Wekan binary tarball` task**  
If deployment fails with a message similar to the following, this is because the role hasn't been updated to reflect the SHA256 hash sum of the latest Wekan release tarball
```
TASK [Wekan.Server : Fetch the Wekan binary tarball] ****************
fatal: [wekan1]: FAILED! => {"changed": false, "failed": true, "msg": "The checksum for /var/lib/wekan/wekan-latest.tgz did not match e6caca890c86f79595da14398dd3ab0c0d3222932d6a8a1b38c6e5082531acd2; it was 21ef5ee220671e5f514f240d1423514c5780d81d6ffba8720d19660079099925."}
```
Right now, maintenance of this variable is a manual process. In the meantime you can simply set this variable's value yourself; via `host_vars`, `group_vars`, `vars` in your playbook, or with `-e` when running `ansible-playbook`, etc.  

Available tags
--------------
To run a specific set of plays, with the `--tags` flag, the available tags are:
- `vars`
- `build`
- `mongodb`
- `repo`
- `nginx`
- `upgrade`
- `service`

Management of the Wekan service
-------------------------------------
This role will deploy a service named `wekan`.
You can use your native service management system to start/stop/reload/restart the service.

Testing via Vagrant
-------------------
To test this role, you'll find a `Vagrantfile` and `provision.yml` playbook in the `tests/` directory.
This is, as you might have guessed, for running test deployments via [Vagrant](https://vagrantup.com).

If you'd like to test some changes, or simply see how the role works/provision a little play Wekan server locally,
you can `cd` into `tests/` and run `vagrant up` (provided you have Vagrant & VirtualBox installed).

If you take a look at the `Vagrantfile`, you'll see there's a deployment for each currently supported platform - simply comment out any you don't want to deploy (don't forget their Ansible config at the bottom, either!).  
Once deployment is finished, if you want to try Wekan out, you can visit `http://localhost:4000` in your browser (the port `4000` varies here, based on which platform you're deploying, see the `forwarded_port` value for your platform).

TODO
----
* [x] Add service user/group to run the Wekan process (for security...)
* [x] Move from PM2 to native service management systems
* [ ] Use Let's Encrypt for SSL

License
-------
MIT

Issues/Contributions
--------------------
Feel free to:  
[Raise an issue](https://github.com/Wekan/Wekan.Ansible/issues)  
[Contribute](https://github.com/Wekan/Wekan.Ansible/pulls)  
