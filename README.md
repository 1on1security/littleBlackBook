# Little Black Book

<img style="float: right;" src="images/myLittleBlackBook.jpg">

We've all done it - consulted the Root of All Knowledge (aka "Google") and long since forgotten the results. Do you find yourself searching for the same things repeatedly? I sure do. I've decided once I've done that three or four times it should end up here for ready reference.  You may also find my saved [installation links](install_links.md) useful.

## Linux - General

#### sudo without password:

Editing /etc/sudoers (use visudo) and adding an entry
such as:

`foo ALL=(ALL:ALL) NOPASSWD: ALL`

... allows user "foo" to execute ALL commands without password. (Do not recommend) ALL can be replaced with the path to a specific executable for refined control.

Example:

`www-data ALL=(ALL:ALL) NOPASSWD: /webthing.bash`

### SSH

#### Client_loop: send disconnect: Broken pipe.:

Annoyed by that persistent disconnect every time you step away for a beverage? A simple fix is found in /etc/ssh/sshd_config. Just a few sprays can provide a swift and lasting solution. The two configuration items of note are ClientAliveInterval and ClientAliveCountMax.

ClientAliveInterval is the period of inactivity after which the SSH server sends an alive message to the remote client that is connected to it. ClientAliveCountMax is the number of attempts that the server will make to send the alive message from the server to the client.

Ubuntu 22.04 for example defaults these to 0 and 3 respectively so you'll find yourself "kicked out" fairly often. Increasing ClientAliveInterval to 300 will increase your timeouts to fifteen minutes.  If those fifteen minutes aren't enough, increase ClientAliveCountMax to 3.

`ClientAliveInterval 300`<br>
`ClientAliveCountMax 3`

## Ubuntu 22.04 LTS - Disable IPV6 Completely

`sudo nano /etc/default/grub`

GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1 quiet splash"<br>
GRUB_CMDLINE_LINUX="ipv6.disable=1"

`sudo update-grub`<br>
`sudo reboot`

## Ubuntu 22.04 LTS disable systemd-resolved

`sudo systemctl disable systemd-resolved`<br>
`sudo systemctl stop systemd-resolved`

## Ansible Crib Notes

`ansible-inventory -i inventory.yaml --list`<br>
`ansible myhosts -m ping -i inventory.ini`<br>
`ansible-playbook -i inventory.yaml playbook.yaml`<br>
`ansible-playbook -i inventory.yaml playbook.yaml --check`  same, but a dry run.

## Connecting to AWS EC2 Instances via AWS EC2 Instance Connect in Ansible

To use AWS EC2 Instance Connect through Ansible, follow these steps.

#### 1. Configure SSH ProxyCommand in `~/.ssh/config`

Edit or create the `~/.ssh/config` file and add the following:

```sh
Host i-*
  IdentityFile /prod/KEYS/your_ssh_key
  User ubuntu
  ProxyCommand aws ec2-instance-connect open-tunnel --instance-id %h
```

This configuration tells SSH to use the aws ec2-instance-connect command whenever connecting to an EC2 instance by its instance ID.

Replace /prod/KEYS/your_ssh_key with your actual private key.

Set the correct User (e.g., ubuntu or ec2-user) based on your EC2 instance.

#### 2. Define your Ansible Inventory File

In your Ansible inventory file, list your EC2 instance IDs as the hostnames. For example:

```ini
[aws_hosts]
i-0796369b8cd3bfc30
```

The inventory uses the EC2 instance IDs as the hostnames, which matches the Host i-* rule in your SSH configuration.

#### 3. Write your Ansible Playbook

In your Ansible playbook, reference the inventory group and set the remote_user to match the SSH configuration.

```---
- hosts: aws_hosts
  remote_user: ubuntu
  tasks:
    - name: Check uptime
      command: uptime
```

## Random

- Revive Stale Bread:
If you've got a loaf of bread that's gone a bit hard, sprinkle it with water and put it in the oven for a few minutes. It'll come out almost as good as fresh.
