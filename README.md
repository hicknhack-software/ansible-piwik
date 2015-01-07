# Ansible Role to install Piwik

This repository provides an up and running role for your Ansible installation. Just clone this repository into the roles directory of your Ansible installation. 

## Installation
1. Clone this repository into your **roles** directory of your Ansible installation via:

   ```git clone https://github.com/hicknhack-software/ansible-piwik.git piwik```
   
   **Don't forget `piwik` after the clone command to ensure the installation into the piwik folder.**
2. Generate a ssh key. And copy your public ssh key (id_rsa.pub) into the parent folder. Name your file *id_rsa.pub*.
3. Add a hosts directory with your host file, e.g. `vagrant` with the following credentials and variables for an example installation on your Vagrant machine:
    
  ```
  [piwik]
  localhost
  
  [piwik:vars]
  ansible_connection=local
  
  # change to your domain where piwik is hosted
  piwik_webpage_url=localhost:8081
  
  # change to the name of the webpage you'd like to track
  # you can add another later in the dashboard of piwik
  piwik_webpage_name=example-webpage 
  
  # change to the domain or ip address of your piwik installation
  piwik_trusted_host=localhost:8081
  
  [mysql:children]
  piwik
  ```

4. Add a folder named `group_vars` as sibling to `roles`. Add a file named `piwik.yml` with the following example content:

  ```
  app_name: piwik
  app_user: deploy
  app_path: /data/piwik
  server_name: stats.example.org
  
  deploy_path: # {{ app_path }}
  deploy_owner: # {{ app_user }}
  deploy_group: # {{ app_user }}
  ```

5. Add a Ansible playbook with the following example content:

  ```
  - hosts: piwik
    sudo: true
    sudo_user: root
    vars:
      provisioned: yes
    roles:
      - role: piwik/prepare/user
        user_authorized_key: "{{ lookup('file', 'id_rsa.pub') }}"
      - piwik/prepare/environment
      - piwik/prepare/mysql
      - piwik/prepare/php
      - piwik/prepare/nginx
  
  - hosts: piwik
    sudo: true
    sudo_user: deploy
    vars:
      gather_facts: true
      profile: '. $HOME/.profile && sh $HOME/.profile && '
      was_provisioned: "{{ provisioned is defined }}"
    roles:
      - role: piwik/install
  ```

6. Run your installation and have fun.

## License

The MIT License (MIT)

Copyright (c) 2015 HicknHack Software GmbH

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
