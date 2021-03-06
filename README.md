# Wundertools

**Note!** This project is no longer in active development and receives only maintenance fixes. For new projects, we recommend <https://github.com/wunderio/drupal-project> instead.

Reference setup with Ansible & Vagrant for Drupal 8 projects. For Drupal 7 support, see the [Drupal 7 branch](https://github.com/wunderio/WunderTools/tree/drupal7).

[![CircleCI](https://circleci.com/gh/wunderio/WunderTools.svg?style=svg)](https://circleci.com/gh/wunderio/WunderTools)

## Requirements
- Install [Vagrant](https://www.vagrantup.com/downloads.html) 1.9.2 or greater
- Install [vagrant-cachier](https://github.com/fgrehm/vagrant-cachier)
 `vagrant plugin install vagrant-cachier`
- Install [Virtualbox](https://www.virtualbox.org/wiki/Downloads) 5.1 or greater. Note version 5.1.24 has a known issue that breaks nfs, do not use it, version 5.1.22 s known to work.
- Make sure you have python2.7 also installedi (For OS X should be default).

## Creating a new project

If you are starting a new project, see: [Setup.md](docs/Setup.md)


## Setting up an existing project locally

Find the IP-address and hostname that this local environment is configured to use from `conf/vagrant_local.yml` and add
it to your own `/etc/hosts` file:

`10.0.13.37 local.wundertools.com`

Let Vagrant create your new machine:

`vagrant up`

This will create a new Virtual machine on your computer, configure it with all the nice bells & whistles that you can
think of (like MariaDB, nginx, Varnish, memcached and whatnot) and start it up for you. This will also install vagrant plugin depedencies, if you encounter issues while installing the plugins then you could use: `vagrant --skip-dependency-manager up`

SSH into your box and build and install Drupal:

```
vagrant ssh
cd /vagrant/drupal
./build.sh new
```

If this is a project with an existing production/staging server, you should probably sync the production database now,
from your local machine:

`sync.sh`

Drush is usable without ssh access with the drush.sh script e.g:

```bash
$ ./drush.sh cr
```

To open up ssh access to the virtual machine:

```bash
$ vagrant ssh
```


## Optional additions

### WunderSecrets

You can setup additional git repository for shared secrets. You need to set that in `conf/project.yml` -> `wundersecrets: remote: git@github.com:username/repo`.

Only the file `ansible.yml` is loaded from that repository.

## Useful things

At the moment IP is configured in
  Vagrantfile
    variable INSTANCE_IP

Varnish responds to
  http://x.x.x.x/

Nginx responds to
  http://x.x.x.x:8080/

Solr responds to
  http://x.x.x.x:8983/solr

MailHOG responds to
  http://x.x.x.x:8025
  or
  https://local.project.tld/mailhog/

Docs are in
        http://x.x.x.x:8080/index.html
        You can setup the dir where the docs are taken from and their URL from the
        variables.yml file.

        #Docs
        docs:
          hostname : 'docs.local.wundertools.com'
          dir : '/vagrant/docs'


## Vagrant + Ansible configuration

Vagrant is using Ansible provision stored under the ansible subdirectory.
The inventory file (which stores the hosts and their IP's) is located under
ansible/inventory. Host specific configurations for Vagrant are stored in
ansible/vagrant.yml and the playbooks are under ansible/playbook directory.
Variable overrides are defined in ansible/variables.yml.

You should only bother with the following:

- Vagrant box setup `conf/vagrant.yml`
- What components do you want to install? `conf/vagrant.yml`
- And how are those set up? `conf/variables.yml`
- You can also fix your vagrant/ansible base setup to certain branch/revision `conf/project.yml`
  There you can also do the same for build.sh


## Debugging tools

XDebug tools are installed via the devtools role. Everything should work out
of the box for PHPStorm. PHP script e.g. drush debugging should also work.

### Lando debugging

XDebug can be enabled by uncommeting `xdebug: true` in the .lando.yml file. After `lando rebuild` port 9000 is used for XDebug.

Note: Make sure port 9000 is not used in your OS for anything else. You can see all ports in use for example with `lsof -i -n -P`. For example php-fpm might be using port 9000 if you have it running.

## Provisioning with Lando

Perform the following tasks in the project root folder to set up the Lando-based provisioning tool:

1. create the file `~/.ssh/ansible.vault` and save it with the Ansible vault password (search for `Ansible vault password` or similar in the LastPass),
2. run `lando start`,
3. use `lando provision` for help and `lando provision <task>` for provisioning tasks.
