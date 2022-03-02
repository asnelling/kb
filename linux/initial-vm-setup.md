---
description: Setup and harden a brand new VM on your choice of cloud provider.
---

# Initial VM Setup

This guide is based on Ubuntu 20.04 LTS hosted at DigitalOcean. Adjust the steps accordingly for your distribution and cloud environment.

1. Log into root (default user)
2. Upgrade all packages\
   `# apt update`\
   `# apt upgrade`
3. Add a new user\
   `# adduser user1`\
   `# usermod -aG sudo user1`\
   `# mkdir /home/user1/.ssh`\
   `# cp .ssh/authorized_keys /home/user1/.ssh/`\
   `# chown -R user1:user1 /home/user1/.ssh`\
   `# chmod -R go-rwx /home/user1/.ssh`
4. Disable root SSH login\
   Create a file in /etc/ssh/sshd\_config.d/ (no\_root\_login.conf in the example below) with the contents:\
   `# /etc/ssh/sshd`\
   `PermitRootLogin no`
5. Reboot to install new kernel (if necessary) and apply sshd changes
6. SSH into the VM using new user
7. Install [fail2ban](http://www.fail2ban.org)\
   `$ sudo apt install fail2ban`
8. Setup [UFW](https://launchpad.net/ufw)\
   `$ sudo ufw default deny incoming`\
   `$ sudo ufw default allow outgoing`\
   `$ sudo ufw allow ssh`\
   `$ sudo ufw enable`
