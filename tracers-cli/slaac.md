# SLAAC Configuration

In the Cisco Packet Tracer, setup the router (1941), switch (2960) and pcs in the below order

```
    router 
        \
        switch
            \
            pc

```

With the Copper Straight Through wires, connect the components

:: Router -> Switch
:: GigabitEthernet 0/0 -> GigabitEthernet 0/1

:: Switch -> PC
:: FastEthernet -> FastEthernet


In the Router CLI type the following
```bash
no

enable
configure terminal

// Turning on ipv6 routing and can also send neighbour advertisement with ICMP6
ipv6 unicast-routing

int g0/0
// Statically configure link local address to be host ONE
ipv6 address fe80::1 link-local

// FOr global Unicast Address - Interface ID
ipv6 address 2001:db8:acad:1::1/64

// turn the interface on
no shut

// router will advertise everyone

```

Then Open Desktop -> IP Configuration in PCS and select Auto-Config.


Note: In the IP Configuration, in the desktop of PC, see that static has a link local address. CLick on Auto Config and experiment is over

