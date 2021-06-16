---
- hosts: all
  become: yes
  tasks:
  - name: update
    apt:
      name: apache2
      update_cache: yes
      state: present
    when: ansible_os_family == "Debian"
  - debug:
     msg: apache2  install on ubuntu
    when: ansible_os_family == "Debian"
  - name: install
    package:
      name: "{{ package_apache }}"
      state: present
  - debug:
       msg: httpd install on redhat
    when: ansible_os_family == "RedHat"
  - name: php
    package:
      name: "{{ item }}"
      state: present
    loop: "{{ php_packages }}"
  - name: copy info.php
    copy:
      content: <?php phpinfo(); ?>
      dest: /var/www/html/info.php
    notify:
    - restart service
  handlers:
    - name: restart service
      service:
        name: "{{ package_apache }}"
        state: restarted
