---
title: Ansible EX294 practice exam and solutions
date: 2021-07-20
authors:
    - jorge
categories:
    - Linux
tags:
    - ansible
    - ex294
    - ex407
    - exam
    - linux
    - practice
    - redhat
    - solutions
    - tips
---
I am hopefully going to take the second half of my RHCE exam this year, which is EX294. I have taken inspiration from  and , and have decided to provide my own answers and explanations as I myself prepare for the exam.

I’ve also only used 4 VM’s in total, 1 controller and 3 nodes, and not 4 nodes as the practice exam suggests.

Here are the tips that I’ve found helpful so far:

<!-- more -->

- **ansible** and **ansible-playbook** share most of it’s syntax. So if it’s **-b** to use **—****become** on **ansible-playbook**, then it’s most likely the same with **ansible**.
- The most important tip of all – use **ansible-doc**! **ansible-doc &lt;module&gt;** (like **ansible-doc user**) will not only give you a list of attributes that you can use, but if you see almost at the bottom of the page, there are examples!
- Make sure that all of the services that are supposed to come back up at boot, do so! Make the services persistent, if the exam says so!
- Remember that if you enable a service, that it doesn’t mean that the change is immediate! Remember to check the **ansible-doc** page for the module if there is an **immediate** attribute that you can use.
- Use the command **ansible all -m setup** after you have set up your inventory to see all of the variables gathered by the facts module. They might come in handy!

Task 1 – Ansible installation and configuration
-----------------------------------------------

Remember that the default location for the original **ansible.cfg** is **/etc/ansible/ansible.cfg**, which you can copy over to your local folder (in this case it’s **/home/automation/plays/**).

### ansible.cfg

These are the fields that I’ve changed in my own **ansible.cfg** file:

```
inventory      = /home/automation/plays/inventory
forks          = 10
roles_path    = /home/automation/plays/roles/
host_key_checking = False
remote_user = automation
become=False
```

### inventory

```
[proxy]
node1           ansible_host=ip.of.the.host

[webserver]
node2           ansible_host=ip.of.the.host

[database]
node3           ansible_host=ip.of.the.host
```

Task 2 – Ad-Hoc commands
------------------------

What they mean by the use of “Ad-Hoc” is your typical **ansible** one liners. Which is to actually use the command **ansible**. I’ve placed the following **ansible** one liners in a file named **adhoc**. Remember to change the permissions on the file (**chmod +x adhoc**) before you run it.

### adhoc

In this particular case I’m connecting as the user **cloud\_user**, which will then use **sudo** to perform the commands.

```
#!/bin/bash

ansible -i inventory all -u cloud_user -b -kK -m user -a \
  "name=automation" --limit node3

ansible -i inventory all -u cloud_user -b -kK -m authorized_key -a \
  "user=automation \
  state=present \
  exclusive=True \
  key=\"ssh-ed25519 AAAADNzaCElZDI1NTE5AAAAIA03SY0CuxcuTSw0gEcNZg8TMLezwjsxnndmn0i1 automation@controller\"" --limit node3

ansible -i inventory all -u cloud_user -b -kK -m lineinfile -a \
  "path=/etc/sudoers \
  line='automation ALL=(ALL) NOPASSWD: ALL' \
  validate=\"/usr/sbin/visudo -cf %s\"" --limit node3
```

The above is exactly the same as the playbook below (which I have named **create\_automation\_user.yml**):

```
---

- hosts: all
  become: yes
  tasks:

    - name: Creating the user automation
      user:
        name: automation

    - name: Add SSH-key to authorized_keys
      authorized_key:
        user: automation
        state: present
        exclusive: True
        key: "{{ item }}"
      with_file:
        - ~/.ssh/id_ed25519.pub

    - name: Add user to sudoers file
      lineinfile:
         path: /etc/sudoers
         line: 'automation ALL=(ALL) NOPASSWD: ALL'
         validate: /usr/sbin/visudo -cf %s

...
```

Task 3 – File content
---------------------

### motd.yml

```
---

- hosts: all
  become: yes

  tasks:

    - name: Change to HAproxy-motd
      copy:
        content: "Welcome to HAProxy server"
        dest: /etc/motd
      when: "'proxy' in group_names"

    - name: Change to Apache-motd
      copy:
        content: "Welcome to Apache server"
        dest: /etc/motd
      when: "'webserver' in group_names"

    - name: Change to MySQL-motd
      copy:
        content: "Welcome to MySQL server"
        dest: /etc/motd
      when: "'database' in group_names"

...
```

Task 4 – Configure SSH server
-----------------------------

### sshd.yml

```
---

- hosts: all
  become: yes

  tasks:

    - name: Set Banner to /etc/motd
      lineinfile:
        path: /etc/ssh/sshd_config
        regex: "^Banner"
        line: "Banner /etc/motd"

    - name: Disable X11Forwarding
      lineinfile:
         path: /etc/ssh/sshd_config
         regex: "^X11Forwarding"
         line: "X11Forwarding no"


    - name: Set MaxAuthTries to 3
      lineinfile:
        path: /etc/ssh/sshd_config
        regex: "^MaxAuthTries"
        line: "MaxAuthTries 3"

...
```

Task 5 – Ansible vault
----------------------

### secret.yml

Use the following command to create the ansible vault file named **secret.yml**:

```
ansible-vault create secret.yml
```

This file should contain:

```
user_password: devops
database_password: devops
```

Use the password **devops** to protect the file you create.

### vault\_key

Create a regular file named **vault\_key** that contains the following:

```
devops
```

Task 6 – Users and groups
-------------------------

This is the most difficult task I’ve encountered so far.

### user\_list.yml

```
---

users:
  - username: alice
  - username: vincent
  - username: sandy
  - username: patrick

...
```

### users.yml

```
---

- hosts: all
  become: yes
  vars_files:
    - ./user_list.yml
    - ./secret.yml

  tasks:

    - name: Add users to the webserver hosts if UID starts with 1
      user:
          name: "{{ item.username }}"
          shell: /bin/bash
          groups: wheel
          append: yes
          password: "{{ user_password | password_hash('sha512') }}"
      with_items: "{{ users }}"
      when:
        - "'webserver' in group_names"
        - "item.uid|string|first == '1'"

    - name: Add users to the database hosts if UID starts with 2
      user:
          name: "{{ item.username }}"
          shell: /bin/bash
          groups: wheel
          append: yes
          password: "{{ user_password | password_hash('sha512') }}"
      with_items: "{{ users }}"
      when:
        - "'database' in group_names"
        - "item.uid|string|first == '2'"

    - name: Create SSH directory for users on webserver hosts
      file:
        path: "/home/{{ item.username }}/.ssh/"
        state: directory
        owner: "{{ item.username }}"
        group: "{{ item.username }}"
        mode: "0700"
      with_items: "{{ users }}"
      when:
        - "'webserver' in group_names"
        - "item.uid|string|first == '1'"

    - name: Create SSH directory for users on database hosts
      file:
        path: "/home/{{ item.username }}/.ssh/"
        state: directory
        owner: "{{ item.username }}"
        group: "{{ item.username }}"
        mode: "0700"
      with_items: "{{ users }}"
      when:
        - "'database' in group_names"
        - "item.uid|string|first == '2'"

    - name: Copy private SSH-key to users on the webserver hosts
      copy:
        src: /home/automation/.ssh/id_ed25519
        dest: "/home/{{ item.username }}/.ssh/id_ed25519"
        owner: "{{ item.username }}"
        group: "{{ item.username }}"
        mode: "0600"
      with_items: "{{ users }}"
      when:
        - "'webserver' in group_names"
        - "item.uid|string|first == '1'"

    - name: Copy private SSH-key to users on the database hosts
      copy:
        src: /home/automation/.ssh/id_ed25519
        dest: "/home/{{ item.username }}/.ssh/id_ed25519"
        owner: "{{ item.username }}"
        group: "{{ item.username }}"
        mode: "0600"
      with_items: "{{ users }}"
      when:
        - "'database' in group_names"
        - "item.uid|string|first == '2'"

...
```

Task 7 – Scheduled tasks
------------------------

### regular\_tasks.yml

```
---

- hosts: all
  become: yes

  tasks:

    - name: Create crontab-record on proxy hosts
      cron:
        name: "Append the output of 'date' to /var/log/time.log"
        minute: "0"
        job: "date >> /var/log/time.log"
      when:
        - "'proxy' in group_names"

...
```

Task 8 – Software repositories
------------------------------

### repository.yml

```
---

- hosts: all
  become: yes

  tasks:

    - name: Add yum repository on database hosts
      yum_repository:
        name: "mysql80-community"
        description: "MySQL 8.0 YUM Repo"
        baseurl: "http://repo.mysql.com/yum/mysql-8.0-community/el/8/x86_64/"
        gpgkey: "http://repo.mysql.com/RPM-GPG-KEY-mysql"
        gpgcheck: yes
        enabled: yes
      when:
        - "'database' in group_names"

...
```

Task 9 – Create and work with roles
-----------------------------------

### mysql.yml

```
---

- hosts: database
  become: yes
  vars_files:
    - secret.yml
  roles:
    - sample-mysql

...
```

### sample-mysql/tasks/main.yml

You can create an empty role, named **sample-mysql**, by running the commands:

```
cd /home/automation/plays/roles/
ansible-galaxy init sample-mysql
```

I decided to go with the package **mysql-server** instead of the **mysql-community-server** that is listed in the Lisenet exam. The contents of the file **sample-mysql/tasks/main.yml**:

```
---
# tasks file for sample-mysql

- name: Ensure partition is created
  parted:
    device: "/dev/nvme1n1"
    number: 1
    state: present

- name: Ensure volumegroup exists
  lvg:
    vg: "vg_database"
    pvs: "/dev/nvme1n1p1"

- name: Create logical volume
  lvol:
    vg: "vg_database"
    lv: "lv_mysql"
    size: "512"

- name: Create an XFS filesystem
  filesystem:
    dev: "/dev/vg_database/lv_mysql"

- name: Mount lv_mysql volume on /mnt/mysql_backups
  mount:
    path: "/mnt/mysql_backups"
    src: "/dev/vg_database/lv_mysql"
    state: present

- name: Ensure mysql-server and firewalld are installed
  yum:
    name: "{{ packages }}"
    state: latest
  vars:
    packages:
      - mysql-server
      - firewalld

- name: Open port 3306 in the firewall
  firewalld:
    port: "3306/tcp"
    permanent: yes
    immediate: yes
    state: enabled

- name: Ensure that service mysqld and firewalld are enabled and started
  service:
    name: "{{ item }}"
    state: started
    enabled: yes
  loop:
    - mysqld
    - firewalld

- name: Set mysql root password
  mysql_user:
    name: root
    password: "{{ database_password }}"
    state: present

- name: Apply my.cnf from a template
  template:
    src: my.cnf.j2
    dest: /etc/my.cnf
    owner: root
    group: root
    mode: 0644

...
```

### sample-mysql/templates/my.cnf.j2

```
[mysqld]
bind_address = {{ ansible_default_ipv4.address }}
skip_name_resolve
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

symbolic-links=0
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

Task 10 – Create and work with roles (some more)
------------------------------------------------

### apache.yml

```
---

- hosts: webserver
  become: yes
  roles:
    - sample-apache

...
```

### sample-apache/handlers/main.yml

You can create an empty role, named **sample-apache**, by running the commands:

```
cd /home/automation/plays/roles/
ansible-galaxy init sample-apache
```

As for the contents of the file **sample-apache/handlers/main.yml**:

```
---
# handlers file for sample-apache

- name: restart_apache
  service:
    name: httpd
    state: restarted
    enabled: yes

...
```

### sample-apache/tasks/main.yml

```
---
# tasks file for sample-apache

- name: Ensure the packages httpd, mod_ssl and php are installed
  yum:
    name: ['httpd', 'mod_ssl', 'php']
    state: latest

- name: Ensure that the service httpd is enabled
  service:
    name: httpd
    state: started
    enabled: yes

- name: Ensure the firewall ports 80 and 443 are open
  firewalld:
    service: "{{ item }}"
    permanent: yes
    immediate: yes
    state: enabled
  with_items:
    - http
    - https

- name: Create index.html from template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
  notify: restart_apache

...
```

Task 11: Download roles from Ansible Galaxy and use them
--------------------------------------------------------

Install the role named **geerlingguy.haproxy** by running these commands:

```
cd /home/automation/plays/
ansible-galaxy install geerlingguy.haproxy
```

It will install the role in the roles-location specified in **ansible.cfg**. In this case, **/home/automation/plays/roles/**.

### haproxy.yml

```
---

- name: Configuring HAProxy
  hosts: proxy
  become: yes
  roles:
    - geerlingguy.haproxy
  vars:
    haproxy_frontend_port: "80"
    haproxy_frontend_mode: "http"
    haproxy_backend_balance_method: "roundrobin"
    haproxy_backend_servers:
      - name: webserver1
        address: ip.of.webserver1:80
      - name: webserver2
        address: ip.of.webserver2:80
  tasks:
     - name: Ensure firewalld is installed
       yum:
         name: firewalld
         state: latest

     - name: Ensure firewalld is enabled and running
       service:
         name: firewalld
         state: started
         enabled: yes

     - name: Ensure firewalld has port 80 opened
       firewalld:
         service: http
         permanent: yes
         immediate: yes
         state: enabled

...
```

Task 12: Security
-----------------

We need to make sure that the package named **rhel-system-roles** is installed on the controller itself (not the nodes!). So run:

```
yum install rhel-system-roles
```

This will install the roles to the folder **/usr/share/ansible/roles/**.

### ansible.cfg

Because the role is installed in a different location than we have specified in our **ansible.cfg**, we need to add this path. So the following line in **ansible.cfg**:

```
roles_path = /home/automation/plays/roles/
```

becomes this:

```
roles_path = /home/automation/plays/roles/:/usr/share/ansible/roles/
```

If you look in the folder **/usr/share/ansible/roles/** now, you’ll see that there is a symlink named **linux-system-roles.selinux**, which points to **rhel-system-roles.selinux**. We will be using the symlink in the next configuration file.

### selinux.yml

```
---

- name: Enable the boolean httpd_can_network_connect
  hosts: webserver
  become: yes
  vars:
    selinux_booleans:
      - name: httpd_can_network_connect
        state: on
        persistent: yes
  roles:
    - linux-system-roles.selinux

...
```

Task 13: Use conditionals to control play execution
---------------------------------------------------

### sysctl.yml

```
---

- hosts: all
  become: yes

  tasks:
    - name: Set vm.swappiness to 10 if RAM > 2GB
      sysctl:
        name: vm.swappiness
        value: "10"
        state: present
      when: "ansible_memtotal_mb >= 2048"

    - name: Report not enough memory
      fail:
        msg: "Server memory less than 2048MB. RAM size {{ ansible_memtotal_mb }}"
      when: "ansible_memtotal_mb < 2048"

...
```

Task 14 – Use archiving
-----------------------

### archive.yml

```
---

- hosts: database
  become: yes

  tasks:
    - name: Create the file database_list.txt
      copy:
        content: "dev,test,qa,prod"
        dest: "/mnt/mysql_backups/database_list.txt"

    - name: Compress the file with gz
      archive:
        path: "/mnt/mysql_backups/database_list.txt"
        dest: "/mnt/mysql_backups/archive.gz"
        format: gz

...
```

Task 15 – Work with Ansible facts
---------------------------------

### facts.yml

```
---

- hosts: database
  become: yes

  tasks:
    - name: Ensure the directory exists
      file:
        path: "/etc/ansible/facts.d/"
        state: directory
        recurse: yes

    - name: Copy content to file in new directory
      copy:
        content: "[sample_exam]\nserver_role=mysql\n"
        dest: "/etc/ansible/facts.d/custom.fact"

...
```

Task 16 – Software packages
---------------------------

### packages.yml

```
---

- hosts: all
  become: yes

  tasks:
    - name: Install packages on proxy hosts
      yum:
        name: ['tcpdump', 'mailx']
        state: latest
      when: "'proxy' in group_names"

    - name: Install packages on database hosts
      yum:
        name: ['lsof', 'mailx']
        state: latest
      when: "'database' in group_names"

...
```

Task 17 – Services
------------------

### target.yml

```
---

- hosts: webserver
  become: yes

  tasks:
    - name: Set default target to multi-user
      file:
        src: "/lib/systemd/system/multi-user.target"
        dest: "/etc/systemd/system/default.target"
        state: link

...
```

Task 18 – Create and use templates to create customised configuration files
---------------------------------------------------------------------------

### server\_list.j2

```
{% for host in groups.all %}
{{ hostvars[host].inventory_hostname }}
{% endfor %}
```

### server\_list.yml

```
---

- hosts: all
  become: yes

  tasks:
    - name: Create template
      template:
        src: "server_list.j2"
        dest: "/etc/server_list.txt"
        owner: automation
        mode: 0600

...
```
