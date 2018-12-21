# DNCS-LAB

# Assigment
Based the `Vagrantfile` and the provisioning scripts available at:
https://github.com/dustnic/dncs-lab the candidate is required to design a functioning network where
any host configured and attached to `router-1` (through `switch` ) can browse a website hosted on
`host-2-c`.

The subnetting needs to be designed to accommodate the following requirement (no need to create
more hosts than the one described in the `vagrantfile`):
- Up to 130 hosts in the same subnet of `host-1-a`
- Up to 25 hosts in the same subnet of `host-1-b`
- Consume as few IP addresses as possible


# Requirements
 - 10GB disk storage
 - 2GB free RAM
 - Virtualbox
 - Vagrant (https://www.vagrantup.com)
 - Internet

# How-to
 - Install Virtualbox and Vagrant
 - Clone this repository
`git clone https://github.com/dustnic/dncs-lab`
 - You should be able to launch the lab from within the cloned repo folder.
```
cd dncs-lab
[~/dncs-lab] vagrant up --provision
```
Once you launch the vagrant script, it may take a while for the entire topology to become available.
 - Now you can verify the status of the 4 VMs using the command
 ```
 [dncs-lab]$ vagrant status      
 ```
 If everything goes weel the terminal will show you                                                                                                                                                          
 ```
Current machine states:

router-1 | running (virtualbox)
--- | ---
router-2 | running (virtualbox)
--- | ---
switch |  running (virtualbox)
--- | ---
host-1-a | running (virtualbox)
--- | ---
host-1- b | running (virtualbox)
--- | ---
host-2-c | running (virtualbox)
```
- Once all the VMs are running verify you can log into all of them:
`vagrant ssh router`
`vagrant ssh switch`
`vagrant ssh host-a`
`vagrant ssh host-b`


# Network Map
  All the device of our Network can be reach using the broadcast address 192.168.168.000 and the subnet mask is 255.255.248.000.
  The Network can also be divid in three Subnetwork. 

  

        +---------------------------------------------------------------------------+
        |                                                                           |
        |                                                                           |
        |                                                                           |
        |                                                                           | eth0
    +------+                    +--------------+                            +---------------+
    |      |                    |              | 192.168.173.1              |               |
    |      |               eth0 |              | eth2                  eth2 |               |
    |  M   +--------------------+   router-1   +----------------------------+    router-2   |
    |  A   |                    |              |               192.168.173.2|               |
    |  N   |                    |              |                            |               |
    |  A   |                    +--------------+                            +---------------+
    |  G   |                   eth1.170 | eth1.171                                    | eth1
    |  E   |            192.168.170.254 | 192.168.171.254                             | 192.168.172.230
    |  M   |                            |                                             |
    |  E   |                            |                                             |
    |  N   |                            | eth1                                        |
    |  T   |               eth0 +---------------+                                     |
    |      +--------------------+               |                                     |
    |      |                    |    SWITCH     |                                     |
    |  V   |                    +---------------+                                     |
    |  A   |                eth2  |           | eth3                                  |
    |  G   |                      |           |                                       |
    |  R   |                      |           |                                       |
    |  A   |                      |           |                                       |
    |  N   |        192.168.170.1 |           | 192.168.171.225                       | 192.168.172.229
    |  T   |                 eth1 |           | eth1                                  | eth1
    |      |          +--------------+       +--------------+                    +------------+
    |      |          |              |       |              |                    |            |
    |      |          |              |       |              |                    |            |
    |      |     eth0 |   host-1-a   |       |   host-1-b   |                    |  host-2-c  |
    |      +----------+              |       |              |                    |            |
    |      |          |              |       |              |                    |            |
    |      |          +--------------+       +--------------+                    +------------+
    |      |                                        | eth0                             | eth0
    |      +----------------------------------------+                                  |
    |      |                                                                           |
    +------+                                                                           |
       |                                                                               |
       +-------------------------------------------------------------------------------+





  In the first (A) there are host-1-a, host-1-b, switch and router-1; This net is split in two vlan. The vlan with the tag 170 link router-1 with host-1-a, on the other side we have vlan 171 that link always router-1 with host-1-b. <br>
  The second (B) Subnetwork link the two router of the topology eachother. <br>
  In the last one, the third (C) the net link router-2 with host-2-c.

## Subnet A
    We'll call subnet A the part of the network in which we can find router1, switch host1a and host 1b. We split the link between the port eth1 of the router and the port eth1 of the switch in two vlan, so we can use the same fisical link to connect two different host through the switch.

    The VLAN that link router1 to host 1-a has 24 bit reserved and only 8 bit for the hostes but its enough for our configuration. In fact we have 2^8 different IP address, but we can't use all it. We have to reserved 2 IP, the first one 192.168.170.0 is for IP address space (it has all the last 8 bit at zero) and the last one is the broadcast address 192.168.170.255 (it has all the last 8 bit at one). We decided to use the first free IP address for the port eth1 of the host-1-a (192.168.170.1)  and the last free for the port eth1.170 of the router (192.168.170.254).


    Its IP address space is 192.168.168.0, the subnet mask is 255.255.175.000 and the broadcast address is 192.168.175.255; so we have 21 bit for the net and we use only 