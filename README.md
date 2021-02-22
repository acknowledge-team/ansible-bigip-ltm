ansible-bigip-ltm
=========

This role is used to configure applications load balancing on F5 bigip appliances.
The idea of this role is to have an inventory host per application.

For each application, the role will proceed to the following configuration : 
- Create a partition for the team in charge of the application
- Create nodes, with default icmp, within created partition
- Create monitors for the default pool
- Create the default pool defined as variable
- Create snat pool or translation object if needed
- Create profiles required on the virtual server : tcp, udp, http and/or ssl
  - For ssl profiles, the role is important ca certificate chain, ssl certificate and key 
- Create virtual server associated with ltm objects listed previously

Requirements
------------

Here are the requirements to use this role : 
- ansible 2.10.7
- ansible-base 2.10.5
- python >3.7

Role Variables
--------------

You'll find below available variables with their default values : 

### BIGIP connection settings

The following variables needs to be defined as they defined the bigip to access and the credentials to use to proceed to the configuration : 

> bigip_hostname: ""

> bigip_user: ""

> bigip_passwd: ""

> bigip_validate_certs: false

### Migration flag

The following variable is used to defined disable arp response for created virtual address; this is useful for example when you are working on a f5 bigip migration : 

> readdressed: true

### Application

This first variable is the name of the application. It's used to define some ltm objects names like the virtual server, the pool, the profiles, ...

> application: "my-super-app"

This `env` variable is used to define the environment of the virtual server (prod, qa, staging, ...). It's used to define some ltm objects names like the virtual server, the pool, the profiles, ...

> env: "prod"

The following variable is the name of the team in charge of the concerned app. It's used to define some ltm objects names like the virtual server, the pool, the profiles, ...

> team: "my-super-team"

The following variable is the route domain id for the application : 

> rd: "5"

### Default pool monitor

> monitor_prefix: "m_"

Should be tcp, udp or http : 

> monitor: "tcp"

````
tcp_monitor_settings:
  interval: 10
  timeout: 31
````

````
udp_monitor_settings:
  interval: 10
  timeout: 31
````

````
http_monitor_settings:
  interval: 10
  timeout: 31
  receive: ""
  send: ""
````

### Nodes and default pool

> node_prefix: "n_"

> pool_prefix: "g_"

````
pool:
  id: "00"
  lb_method: "round-robin"
  priority_group_activation: 0
  members:
    - { "id": "00", "name": "node-1", "ip": "1.1.1.1", "port": 80, "priority_group": 1, "ratio": 1 }
    - { "id": "01", "name": "node-2", "ip": "1.1.1.2", "port": 80, "priority_group": 1, "ratio": 1 }
````

### Profiles

> profile_prefix: "p_"

Enabling default http profile creation for the application : 

> enable_http_profile: false

Enabling default tcp profile creation for the application : 

> enable_tcp_profile: true

Enabling default udp profile creation for the application : 

> enable_udp_profile: false

Enabling default ssl client profile creation for the application : 

> enable_clientssl_profile: false
> ssl_ca_chain: "ca-acknowledge"
> ssl_ca_chain_filename: "ca.crt"

### SNAT

> snat_prefix: "snat_"

Used to enable or disable snat if you want to have snat pool or snat translation : 

> snat: false

Should be pool or translation : 

> snat_type: "pool" 

Used to define snat pool members : 

> snat_pool_members: [ "10.123.2.106", "10.123.2.107" ]

Used for snat translation address : 

> snat_translation: "10.123.2.106" 

### Virtual Server

> vs_prefix: "v_"

````
vs:
  address: "10.123.4.35"
  port: 80
  type: "standard"
````

Dependencies
------------

N/A

Example Playbook
----------------

````
- hosts: all
  connection: local 
  roles:
    - ansible-bigip-ltm
````

License
-------

BSD