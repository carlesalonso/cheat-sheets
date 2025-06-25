# Cisco IOS

## Working with console cables

```console
sudo screen /dev/ttyUSB0 9600
```

## Information

| Task                    | Command                     |
| :---------------------- | :-------------------------- |
| Show IPv4 settings      | `show ip interface brief`   |
| Show IPv6 settings      | `show ipv6 interface brief` |
| Show routing table      | `show ip route`             |
| Show config             | `show running-config`       |
| Show VLAN (switch) info | `show vlan`                 |

## General configuration

Getting started:

```
Router>enable
Router#configure terminal
Router(config)#
```

## `config-if`

Set an IPv6 address:

```
Router(config)#interface G0/0/0
Router(config-if)#ipv6 address 2001:DB8:0:1::/64
Router(config-if)#ipv6 enable
Router(config-if)#no shutdown
Router(config-if)#exit
```

| Task                              | Command     |
| :-------------------------------- | :---------- |
| Enable crossover cable autodetect | `mdix auto` |

## Load config from TFTP server

- `enable`
- On (some) routers: `ip tftp source-interface INTERFACE`
- `copy tftp run`
- Address of remote host: ...
- Source filename: ...
- Destination filename: running-config

Example:

```console
#copy tftp running-config
Address or name of remote host []? IP_ADDRESS
Source filename []? FILENAME
Destination filename [running-config]? 
Accessing tftp://IP_ADDRESS/FILENAME...
Loading FILENAME from IP_ADRESS (via INTERFACE): !
[OK - 1273 bytes]
...
```

## Routing

### IPv4 static routing

| Task             | Command                               |
| :--------------- | :------------------------------------ |
| Set default GW   | `ip route 0.0.0.0 0.0.0.0 GW_ADDR`    |
| Route to network | `ip route NET_ADDR NET_MASK NEXT_HOP` |

### IPv6 static routing

| Task                           | Command                                    |
| :----------------------------- | :----------------------------------------- |
| Enable routing                 | `ipv6 unicast-routing`                     |
| Directly attached static route | `ipv6 route 2001:DB8::/32 g1/0/0`          |
| Recursive static route         | `ipv6 route 2001:DB8::/32 2001:DB8:3000:1` |

Resources:

- <https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_pi/configuration/xe-3s/iri-xe-3s-book/ip6-route-static-xe.html>
- Example: <https://www.cisco.com/c/en/us/support/docs/ip/ip-version-6-ipv6/113361-ipv6-static-routes.html>

### Enable NAT

- Internal interface (in config-if): `ip nat inside`
- External interface (in config-if): `ip nat outside`
- Enable NAT:
    - `ip nat inside source list 1 interface G0/0/0 overload`
    - `access-list 1 permit NET_ADDR WILDCARD`, e.g.
        - `access-list 1 permit 192.168.0.0 0.0.0.255`

### Enable DHCP

```console
Router(config)#ip dhcp pool POOL_NAME
Router(dhcp-config)#network NET_ADDR NET_MASK
Router(dhcp-config)#default-router GW_ADDR
Router(dhcp-config)#dns-server DNS_ADDR
Router(dhcp-config)#ip dhcp excluded-address START END
```

- Set tftp server: `option 150 ip TFTP_ADDR`

## Switch

- Useful commands
    - `show ip int br`
    - `show ip ?` -> shows possible expansions of an incomplete command
- Assigning an IP address to a switch
    - `interface vlan 1`
    - `ip address 192.168.200.13 225.225.225.248`
    - `no shutdown`
    - `end`
- Reset to defaults (assuming no persistent configuration):
    - `delete flash:vlan.dat`: VLAN-settings verwijderen
    - `erase startup-config`: alle manuele instellingen verwijderen
    - `reload`
