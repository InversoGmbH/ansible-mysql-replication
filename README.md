Role Name
=========

A mysql master-master replication is actually a master-slave replication in both directions
This role has to be called once per host wich gets added the role of a slave to the existing master-role

Requirements
------------

This role requires geerlingguy.mysql which initially configures a mysql master

Role Variables
--------------

Dependencies
------------

Example Playbook
----------------

    - name: setup mysql master 1
      hosts: server1.domain.local
      roles:
        - role: geerlingguy.mysql
          mysql_root_password: 'root'
          mysql_replication_user:
            name: 'replicator'
            password: 'repl1234'
          mysql_server_id: '1'
          mysql_replication_role: 'master'

    - name: setup mysql master 2
      hosts: server2.domain.local
      roles:
        - role: geerlingguy.mysql
          mysql_root_password: 'root'
          mysql_replication_user:
            name: 'replicator'
            password: 'repl1234'
          mysql_server_id: '2'
          mysql_replication_role: 'master'

    - name: setup replication slave 1
      hosts: server1.domain.local
      roles:
        - role: inverso.mysql-replication
          mysql_replication_user:
            name: 'replicator'
            password: 'repl1234'
          mysql_replication_master: 'server2.domain.local'

    - name: setup replication slave 2
      hosts: server2.domain.local
      roles:
        - role: inverso.mysql-replication
          mysql_replication_user:
            name: 'replicator'
            password: 'repl1234'
          mysql_replication_master: 'server1.domain.local'

By using group_vars and host_vars this playbook can be reduced to 1 play with two roles

    - name: setup mysql servers
      hosts: mysql
      roles:
        - role: geerlingguy.mysql
        - role: inverso.mysql-replication

    The group_vars should contain:
    mysql_replication_user:
      name: 'replicator'
      password: 'repl1234'
    
    The host_vars should contain in server1.domain.local.yaml
    mysql_server_id: '1'
    mysql_replication_master: 'server2.domain.local'
    
    The host_vars should contain in server2.domain.local.yaml
    mysql_server_id: '2'
    mysql_replication_master: 'server1.domain.local'


License
-------


Author Information
------------------

