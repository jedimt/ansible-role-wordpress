Ansible Role: Wordpress
=========

Installs a sample WordPress site with persistent volumes for SQL data.

Requirements
------------

Requires kubernetes python module and the Kubernetes collection
  - python -m pip install kubernetes
  - ansible-galaxy collection install kubernetes.core

Role Variables
--------------

    # Set wordpress to present | absent
    state: present

    # These versions are old, but newer versions have "Error establishing a database connection"
    # Would need to investigate what needs to change to fix that with WP 5.x and MySQL 8.x
    wordpress_image: wordpress:4.8-apache
    mysql_image: mysql:5.6

    # Storage class to use for CSI persistent volumes
    storage_class: "nebulon"

Dependencies
------------

A dynamic provisioner for CSI volumes that can respond to PVC requests.

Example Playbook
----------------

    - name: Wordpress Install
      hosts: k8s_master

      tasks:
        - name: Copy kubeconfig to local machine
          ansible.builtin.fetch:
            src: /etc/kubernetes/admin.conf
            dest: ~/.kube/{{ inventory_hostname }}-config
            flat: true
          tags: kubeconfig

        - name: Set kubeconfig variable
          ansible.builtin.set_fact:
            kubeconfig: ~/.kube/{{ inventory_hostname }}-config
          changed_when: false
          tags: kubeconfig

    - name: Install WordPress application
      hosts: localhost
      connection: local

      roles:
        - { role: jedimt.wordpress }

License
-------

MIT

Author Information
------------------

Aaron Patten
aaronpatten@gmail.com
