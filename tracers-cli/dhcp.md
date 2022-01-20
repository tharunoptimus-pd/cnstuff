# DHCP Configuration

In the Cisco Packet Tracer, setup the routers 2620XM, 2960-24TT switch and some pcs in the below order

```
        router
          |
        switch
        |  |  |
       /   |   \
       pc  pc  pc
    
```

Connect all the components with Copper Straight Through wires and connect with Fast Ethernet. Doesn't matter what ethernet it is connected with. 

NOTE: Router has only one Fast Ethernet 0/0. 

Open the CLI for the router and enter the commands one by one

```sh
no

enable
conf t
host R1

int fa0/0
ip add 192.168.10.1 255.255.255.0
no shut

exit

ip dhcp pool IP10

net 192.168.10.0 255.255.255.0
default 192.168.10.1
exi

ip dhcp exc 192.168.10.1 192.168.10.10 ?

ip dhcp exc 192.168.10.1 192.168.10.10
exit

copy run start

// check settings
sh run

exit

```

Then open the PCS -> Desktop -> IP Configuration and select DHCP

It will automatically resolve the IPs. Do that for all PCs.