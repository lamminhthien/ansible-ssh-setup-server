# Install Docker and Nginx
```sh
ansible-playbook -i inventory.ini install-docker-and-nginx.yml --ask-become-pass
```

# Add Nginx template to point to port with domain base on Ansible params
```sh
ansible-playbook -i inventory.ini setup-domain-with-nginx.yml --ask-become-pass
```