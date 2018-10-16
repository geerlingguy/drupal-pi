# Drupal Pi

[![Build Status](https://travis-ci.org/geerlingguy/drupal-pi.svg?branch=master)](https://travis-ci.org/geerlingguy/drupal-pi)

**Drupal on a single Raspberry Pi**

<p align="center"><img src="https://raw.githubusercontent.com/geerlingguy/drupal-pi/master/images/drupal-pi-model-2.jpg" alt="Drupal 8 on a Raspberry Pi" /></p>

This project is an offshoot of the [Rasbperry Pi Dramble](https://github.com/geerlingguy/raspberry-pi-dramble) project, which helps install Drupal on a cluster ('Bramble') of Raspberry Pi 2 computers.

This playbook/project makes setting up Drupal on a _single_ Raspberry Pi a very easy/simple operation.

## Set up the Raspberry Pi

Drupal requires as good a Raspberry Pi as you can afford. While Drupal will run okay on any Raspberry Pi, it's best to use a B model 2 or model 3 (both have a snappy four-core processor and 1GB RAM).

Once you have your Raspberry Pi and a good microSD card (the fastest/best one you can get—see [microSD Card Benchmarks](http://www.pidramble.com/wiki/benchmarks/microsd-cards)!), you will need to do a few things to set up the Raspberry Pi and get it ready to run the Drupal installation playbook.

### Set up on Raspberry Pi with Raspbian / GUI

These directions assume you're working directly on your Raspberry Pi, running Raspbian, with a keyboard and monitor attached:

  1. Download the latest 'Raspbian' image from the [Raspberry Pi Downloads page](https://www.raspberrypi.org/downloads/)†.
  1. Follow the [image installation guide](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) to transfer the image to your microSD card:
     1. Unmount the microSD card: `diskutil unmountDisk /dev/disk2`
     1. Write the image to the microSD card: `pv yyyy-mm-dd-raspbian-stretch.img | sudo dd of=/dev/rdisk2 bs=1m`
  1. Once Raspbian is loaded on the card, insert the card in your Pi, and plug in your Pi to boot it up.
  1. Boot up the Raspberry Pi. Once booted, open the "Raspberry Pi Configuration" tool in Menu > Preferences.
     1. Change the pi user account password.
     2. Click OK, then reboot the Raspberry Pi.
  1. Once rebooted, connect the Pi to your local network either via WiFi or wired ethernet.
  1. Open the Terminal application (in the launcher or in Menu > Accessories > Terminal).
  1. Install Ansible: `sudo apt-get update && sudo apt-get install -y python-dev python-pip libffi-dev && sudo pip install ansible`
  1. Test the Ansible installation: `ansible --version` (should output the Ansible version).

*† If you plan on using your Pi as a headless Drupal server, you don't need all the extra software included with the default Raspbian image. I recommend you use the official 'Raspbian Lite' image instead; see the next section.*

### Set up on Raspberry Pi with Raspbian Lite / CLI

These directions assume you're working either directly on your Raspberry Pi, running Raspbian Lite, or remotely logged into the Pi via SSH:

  1. Download the latest 'Raspbian Lite' image from the [Raspberry Pi Downloads page](https://www.raspberrypi.org/downloads/)†.
  1. Follow the [image installation guide](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) to transfer the image to your microSD card:
     1. Unmount the microSD card: `diskutil unmountDisk /dev/disk2`
     1. Write the image to the microSD card: `pv yyyy-mm-dd-raspbian-stretch-lite.img | sudo dd of=/dev/rdisk2 bs=1m`
     1. Ensure SSH is enabled by adding an 'ssh' file to the boot volume: `touch /Volumes/boot/ssh`
  1. Once Raspbian Lite is loaded on the card, insert the card in your Pi, and plug in your Pi to boot it up.
  1. Boot up the Raspberry Pi. Once booted, log in (default username is `pi` and default password is `raspberry`), and run `sudo raspi-config`.
     1. Set a better password for the Pi's default user account.
     1. Scroll down to 'Finished', hit return, and reboot the Raspberry Pi.
  1. Once rebooted, connect the Pi to your local network either via [WiFi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/setting-up-wifi-with-occidentalis) or wired ethernet.
  1. Log back in (either on the Pi directly or via SSH).q
  7. Install Git and Ansible: `sudo apt-get update && sudo apt-get install -y python-dev python-pip libffi-dev git && sudo pip install ansible`
  1. Test the Ansible installation: `ansible --version` (should output the Ansible version).

## Install LEMP software stack and Drupal

### Installing using the Raspberry Pi

You need to download this repository to the Pi and run the included playbook to install and configure everything.

  1. Clone the `drupal-pi` project: `git clone https://github.com/geerlingguy/drupal-pi.git && cd drupal-pi`
  1. Copy `example.inventory` to `inventory`.
  1. (Optional) Create a `config.yml` and override any settings from `default.config.yml` as needed.
  1. Install required Ansible roles: `ansible-galaxy install -r requirements.yml`
  1. Run the Ansible playbook: `ansible-playbook -i inventory -c local main.yml`

After a few minutes, the playbook should complete successfully, and you should have Drupal running on your Raspberry Pi, accessible via `http://localhost/`

To be able to access the site from other computers on your network (e.g. by accessing `http://www.drupalpi.test/`, [add an entry to your local hosts file](http://www.rackspace.com/knowledge_center/article/how-do-i-modify-my-hosts-file) like `[ip-of-raspberry-pi]  www.drupalpi.test`.

> **NOTE**: For best security, you should create a `config.yml` file and at least override any `password` settings from the `default.config.yml` file (e.g. `mysql_root_password` and `drupal_db_password`).

### Installing using another host with Ansible installed

You can run the Ansible playbook from another host (instead of from within the VM—this also allows you to do everything without installing `pip` and `ansible` on the Raspberry Pi itself!).

  1. Change the `inventory` file to use the Pi's IP address instead of `127.0.0.1`.
  1. Make sure you have your SSH private key configured for the `pi` account on the Pi (I use `ssh-copy-id` to copy my ID to the Pi).
  1. Install required Ansible roles: `ansible-galaxy install -r requirements.yml`
  1. Run the Ansible playbook: `ansible-playbook -i inventory main.yml`

Note: If you have a headless Raspberry Pi and would like to find it's IP address, one way of doing so is to use a tool like [Fing](https://www.fingbox.com/features)).

## Updating your Pi (for future versions of Drupal Pi)

If you need to update Drupal Pi, do the following:

  1. cd into the project directory: `cd /path/to/drupal-pi`
  2. Pull the latest changes: `git pull`
  3. Update all required Ansible roles (and install new ones): `sudo ansible-galaxy install -r requirements.yml --force`
  4. Run the Ansible playbook: `ansible-playbook -i inventory -c local main.yml`

_Note_: Remove `-c local` if running from another host.

## Resetting the Drupal Install

There is a `reset.yml` playbook included that will reset the environment so you can install a fresh copy of Drupal. To run the playbook, enter the following command in the same directory as this README:

    ansible-playbook -i inventory -c local reset.yml

_Note_: Remove `-c local` if running from another host.

After it finishes resetting the environment, you can run the `main.yml` playbook again to rebuild the Drupal site.

## Author

This project was started in 2015 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
