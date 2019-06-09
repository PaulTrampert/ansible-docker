Role Name
=========

Configures Docker CE on an Ubuntu or ArchLinux host, optionally also configuring swarm.

Requirements
------------

No requirements.

Role Variables
--------------

| Variable                 | Type            | Default                            | Description                                                              |
|--------------------------|-----------------|------------------------------------|--------------------------------------------------------------------------|
| swarm_enable             | yes\|no         | no                                 | Configure docker in swarm mode or standalone                             |
| swarm_role               | manager\|worker | manager                            | If in swarm mode, configures whether to be manager or worker             |
| swarm_advertise_addr     | IP[:Port]       | {{ansible_default_ipv4.address}}   | The swarm advertise address                                              |
| swarm_join               | yes\|no         | no                                 | If yes, joins an existing swarm                                          |
| swarm_join_host          | IP              | ''                                 | The host to use when joining an existing swarm                           |
| swarm_join_port          | Port Number     | 2377                               | The port to join on when joining an existing swarm                       |
| swarm_manager_join_token | String          | ''                                 | The manager join token. If creating a swarm, will be set in the hostvars |
| swarm_worker_join_token  | String          | ''                                 | The worker join token. If creating a swarm, will be set in the hostvars  |

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```yml
- hosts: all
  name: Gather facts from all hosts
  tasks: []

- hosts: leader
  become: yes
  become_user: root
  vars:
    ansible_python_interpreter: /usr/bin/python3 # Force ansible to use python3 - Ubuntu doesn't ship with 2 by default
    swarm_enable: yes
    swarm_advertise_addr: "{{ansible_default_ipv4.address}}:2377"
  roles:
    - name: ansible-docker

- hosts: managers
  become: yes
  become_user: root
  vars:
    ansible_python_interpreter: /usr/bin/python3
    swarm_enable: yes
    swarm_join: yes
    swarm_advertise_addr: "{{ansible_default_ipv4.address}}:2377"
    swarm_join_host: "{{hostvars['leader']['ansible_default_ipv4']['address']}}"
    swarm_manager_join_token: "{{hostvars['leader']['swarm_manager_join_token']}}"
  roles:
    - name: ansible-docker

- hosts: workers
  become: yes
  become_user: root
  vars:
    ansible_python_interpreter: /usr/bin/python3
    swarm_enable: yes
    swarm_role: worker
    swarm_join: yes
    swarm_advertise_addr: "{{ansible_default_ipv4.address}}:2377"
    swarm_join_host: "{{hostvars['leader']['ansible_default_ipv4']['address']}}"
    swarm_worker_join_token: "{{hostvars['leader']['swarm_worker_join_token']}}"
  roles:
    - name: ansible-docker
```

License
-------

MIT
