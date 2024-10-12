# Install Docker and Nginx
```sh
ansible-playbook -i inventory.ini install-docker-and-nginx.yml
```

# Add Nginx template to point to port with domain base on Ansible params
```sh
ansible-playbook -i inventory.ini setup-domain-with-nginx.yml
```

# More useful example, please search in Ansible Galaxy