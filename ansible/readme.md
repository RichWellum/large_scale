# What this does #
    These are playbooks to spin up multiple Nova compute containers, along
    with neutron OVS containers, enabling ports and interfaces to mock
    large scale.
    
    This is designed to work on an existing installed CBIS.

# Using #

  * On an existing undercloud clone this repo.
  * Enter 'large_scale' and execute the command: ansible-playbook
    site.yml
  * Note that ansible must be installed first

## Modifying ##
  * Under large_scale/ansible/site.yml modify the variable to
    determine the number of computes to spin up: 
  vars:
    num_nova_fake_per_node: 300
  * hosts file should not need to be changed as it uses explicity
    names that are already mapped to nodes in /etc/ansible/hosts

# Setup notes #

The following had to be managed by hand:


  * Add option to dockerd in /etc/sysconfig/docker:
  OPTIONS="--enable-secrets=false (this secrets thing is a bug in RH
  Docker)
  * chmod a+rwx /var/log/large_scale - because nova fake had some
    problem around accessing the logs
  * disable real compute/ovs-agent on c_ovs 0
  * Remove NUMAfilter and Diskfilter from nova scheduler config
  * restart nova_scheduler container on Controller

# Verifying #

## Undercloud ##

    openstack compute agent list
    openstack compute service list
    openstack network agent list
    more?

## compute o 0 ##
    [cbis-admin@overcloud-ovscompute-0 ~]$ ip a | wc -l
    646

# Testing #


  * it would be good to understand from where we can get like cpu busy
  stats, iowaits, other stuff
  * maybe Kibana already has those metrics from Zabbix or something,
  but it would be good to find out
  * so then together with rally test, we should check OS metrics, to
    understand what is the bottleneck

# ToDo #

  * Investigate why fake computes don't get registered in
  placement/cell - workaround now is to discover computes using
  nova-manage in nova_api docker
  * Enabled pipelining in ansible.cfg - should be faster overall
  * Investigate why after spawning a fake compute/ovsagent, we restart
  it also - probably there is a notify from task that ensures it's
  started to restarting - if there is one, just delete it
  * Move some stuff into variables
  * Test on a clean slate - e.g. remove /var/log/large_scale and
  /etc/large_scale
  * Make a role to clean up stuff, I guess linked to 5)
  * And of course the rally stuff
