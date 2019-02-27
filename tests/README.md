# Vagrant testing configuration

This Vagrantfile can be used to test the Drupal Pi configuration locally if you do not have access to a Raspberry Pi.

To test locally:

  1. Install required Ansible roles (up one directory): `ansible-galaxy install -r requirements.yml --force`
  1. Start and provision a local VM (in this directory): `vagrant up`
  1. Visit `http://local.drupalpi.test/` and follow the Drupal installer steps to install Drupal.
