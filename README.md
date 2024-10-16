## Ansible a tool use ssh connection will help us automation install, config software, add cron job, add config domain to nginx,...

### Note: Before doing this tutorial, take note about installing Debian with VMware and configuring to use SSH.

Login as root

```sh
sudo su -
```

Open the SSH configuration file

```sh
nano /etc/ssh/sshd_config
```

```
# Allow password authentication
PasswordAuthentication yes

# Allow public key authentication
PubkeyAuthentication yes

# Permit root login (not recommended for production)
PermitRootLogin yes

# Specify which users can log in (optional)
AllowUsers your_username

# Listen on default SSH port
Port 22
```

Save and exit the editor (Ctrl + X, then Y, then Enter)

Restart the SSH service to apply changes

```sh
systemctl restart sshd
```

Check the VM or server IP Address

```sh
ip addr show
```

Great choice! Ansible is a powerful and user-friendly tool for automation. Here's a step-by-step guide to get you started with Ansible on macOS:

1. Install Ansible:
   The easiest way to install Ansible on macOS is using Homebrew. If you don't have Homebrew installed, you can install it first.

   Open Terminal and run:

```sh
brew install ansible hudochenkov/sshpass/sshpass
```

2. Verify the installation:

   ```
   ansible --version
   ```

3. Create a directory for your Ansible project:

```sh
mkdir ~/ansible-project
cd ~/ansible-project
```

4. Create an inventory file:

```sh
touch inventory.ini
```

Edit this file and add your server's IP or hostname:

```yaml
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[dbservers]
db1 ansible_host=192.168.1.20

# For authorize server by ssh key
#[all:vars]
#ansible_user=your_ssh_user
#ansible_ssh_private_key_file=/path/to/your/private/key
#ansible_become=yes
#ansible_become_method=sudo

# For authorize server by ssh password
[all:vars]
ansible_user=root
ansible_ssh_pass=aaaa
ansible_become=yes
ansible_become_method=sudo
```

5. Create an Ansible playbook:

```sh
touch install-docker-and-nginx.yml
```

6. Edit the install-docker-and-nginx.yml file to include tasks for installing Docker, Docker Compose, and Nginx. Here's a basic example:

```yaml
---
- name: Install Docker, Nginx, and Certbot
  hosts: all
  become: yes

  tasks:
    - name: Add Docker GPG key
      ansible.builtin.apt_key:
        url: https://download.docker.com/linux/debian/gpg
        state: present

    - name: Add Docker repository
      ansible.builtin.apt_repository:
        repo: "deb [arch=amd64,arm64] https://download.docker.com/linux/debian {{ ansible_distribution_release }} stable"
        state: present

    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: yes

    - name: Install Docker
      ansible.builtin.apt:
        name: docker-ce
        state: present

    - name: Install Nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Install Certbot and Nginx plugin
      ansible.builtin.apt:
        name: 
          - certbot
          - python3-certbot-nginx
        state: present
        update_cache: yes

    - name: Create cron job for Certbot auto-renewal
      cron:
        name: "Auto renew SSL certificates"
        job: "0 0 * * * /usr/bin/certbot renew --quiet && systemctl reload nginx"
        state: present

    - name: Start and enable Docker service
      ansible.builtin.service:
        name: docker
        state: started
        enabled: yes

    - name: Start and enable Nginx service
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: yes

```

7. Run the playbook:

```sh
ansible-playbook -i inventory.ini install-docker-and-nginx.yml --ask-become-pass
```

This playbook assumes you're using an Ubuntu-based server. You might need to adjust it based on your specific server OS.

A few things to note:

- Ensure you have SSH access to your server.
- The `--ask-become-pass` flag will prompt for the sudo password on the remote server.
