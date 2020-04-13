# I. Toplogie 1 - intro VLAN

## 2.Setup clients

## 🌞 Tout ce beau monde doit se ping

### les admins se joignent entre eux

- [admin3>] ping 10.5.10.12
  84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.713 ms
  84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=1.367 ms
  84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.881 ms
  84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=1.060 ms
  84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.832 ms

### les guests se joignent entre eux

- guest1> ping 10.5.20.12
  84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=1.084 ms
  84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.895 ms
  84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=1.557 ms
  84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=1.019 ms
  84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=1.489 ms

## 3.Setup VLANs

## 🌞 Mettez en place les VLANs sur vos switches

### les ports vers les clients (admins et guests) sont en access

IOU1#show vlan brief

VLAN Name Status Ports

---

1 default active Et0/1, Et0/2, Et0/3, Et1/1
Et1/2, Et1/3, Et2/1, Et2/2
Et2/3, Et3/0, Et3/1, Et3/2
Et3/3
10 admins active Et1/0
20 guest active Et2/0
1002 fddi-default act/unsup
1003 token-ring-default act/unsup
1004 fddinet-default act/unsup
1005 trnet-default act/unsup

---

IOU2#show vlan brief

VLAN Name Status Ports

---

1 default active Et0/1, Et0/2, Et0/3, Et1/1
Et1/2, Et1/3, Et2/1, Et2/2
Et2/3, Et3/0, Et3/1, Et3/2
Et3/3
10 admins active Et1/0
20 guests active Et2/0
1002 fddi-default act/unsup
1003 token-ring-default act/unsup
1004 fddinet-default act/unsup
1005 trnet-default act/unsup

### les ports qui relient deux switches sont en trunk

IOU1#show interface trunk

Port Mode Encapsulation Status Native vlan
Et0/0 on 802.1q trunking 1

Port Vlans allowed on trunk
Et0/0 1-4094

Port Vlans allowed and active in management domain
Et0/0 1,10,20

Port Vlans in spanning tree forwarding state and not pruned
Et0/0 1,10,20

---

IOU2#show int trunk

Port Mode Encapsulation Status Native vlan
Et0/0 on 802.1q trunking 1

Port Vlans allowed on trunk
Et0/0 1-4094

Port Vlans allowed and active in management domain
Et0/0 1,10,20

Port Vlans in spanning tree forwarding state and not pruned
Et0/0 1,10,20

## 🌞 Vérifier que

### les guests peuvent toujours se ping, idem pour les admins

- guest1> ping 10.5.20.12
  84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.326 ms
  84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=4.196 ms
  84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.494 ms
  84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.461 ms
  84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.434 ms

* [admin3>] ping 10.5.10.12
  84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.286 ms
  84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.460 ms
  84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.543 ms
  84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.431 ms
  84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.402 ms

### montrer que si un des guests change d'IP vers une IP du réseau admins, il ne peut PAS joindre les autres admins

- guest1> ip 10.5.10.13
  Checking for duplicate address...
  PC1 : 10.5.10.13 255.255.255.0

- guest1> ping 10.5.10.11
  host (10.5.10.11) not reachable

# II. Topologie 2 - VLAN, sous-interface, NAT

## 2.Adressage

## 🌞 Vérifier que :

### les guests peuvent ping les autres guests

- guest3> ping 10.5.20.11
  84 bytes from 10.5.20.11 icmp_seq=1 ttl=64 time=0.412 ms
  84 bytes from 10.5.20.11 icmp_seq=2 ttl=64 time=0.816 ms
  84 bytes from 10.5.20.11 icmp_seq=3 ttl=64 time=0.550 ms
  84 bytes from 10.5.20.11 icmp_seq=4 ttl=64 time=0.664 ms
  84 bytes from 10.5.20.11 icmp_seq=5 ttl=64 time=0.572 ms

### les admins peuvent ping les autres admins

- admin3> ping 10.5.10.11
  84 bytes from 10.5.10.11 icmp_seq=1 ttl=64 time=0.557 ms
  84 bytes from 10.5.10.11 icmp_seq=2 ttl=64 time=0.661 ms
  84 bytes from 10.5.10.11 icmp_seq=3 ttl=64 time=0.592 ms
  84 bytes from 10.5.10.11 icmp_seq=4 ttl=64 time=0.653 ms
  84 bytes from 10.5.10.11 icmp_seq=5 ttl=64 time=2.635 ms

## 3.VLAN

## 🌞 Configurez les VLANs sur l'ensemble des switches

conf t

(config)# vlan 10
(config-vlan)# name admins
(config-vlan)# exit

(config)# vlan 20
(config-vlan)# name guests
(config-vlan)# exit

(config)# interface ethernet <interface lié aux guests et admins>
(config-if)# switchport mode access
(config-if)# switchport access vlan <10 pour admins et 20 pour guests>
(config-if)# exit

## 🌞 Vérifier et prouver qu'un guest qui prend un IP du réseau admins ne peut joindre aucune machine.

- guest3> ip 10.5.10.14
  Checking for duplicate address...
  PC1 : 10.5.10.14 255.255.255.0

- guest3> ping 10.5.10.11
  host (10.5.10.11) not reachable

* guest2> ip 10.5.10.14
  Checking for duplicate address...
  PC1 : 10.5.10.14 255.255.255.0

* guest2> ping 10.5.10.11
  host (10.5.10.11) not reachable

- guest1> ip 10.5.10.14
  Checking for duplicate address...
  PC1 : 10.5.10.14 255.255.255.0

- guest1> ping 10.5.10.11
  host (10.5.10.11) not reachable

## 🌞 Configurez les sous-interfaces de votre routeur

conf t
(config)# interface fastEthernet1/0.10
(config-subif)# encapsulation dot1Q 10
(config-subif)# ip address 10.5.10.254 255.255.255.0
(config-subif)# exit
(config)# interface fastEthernet1/0.20
(config-subif)# encapsulation dot1Q 20
(config-subif)# ip address 10.5.20.254 255.255.255.0
(config-subif)# exit
(config)# interface fastEthernet1/0
(config)# no shut
(config-if)# exit

## 🌞 Vérifier que les clients et les guests peuvent maintenant ping leur passerelle respective

- guest2> ping 10.5.20.254
  84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=20.151 ms
  84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=6.848 ms
  84 bytes from 10.5.20.254 icmp_seq=3 ttl=255 time=5.083 ms
  84 bytes from 10.5.20.254 icmp_seq=4 ttl=255 time=6.994 ms
  84 bytes from 10.5.20.254 icmp_seq=5 ttl=255 time=8.788 ms

- guest2> ip 10.5.10.15
  Checking for duplicate address...
  PC1 : 10.5.10.15 255.255.255.0

- guest2> ping 10.5.10.254
  host (10.5.10.254) not reachable

- guest2> ping 10.5.10.254
  84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=9.838 ms
  84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=6.406 ms
  84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=5.883 ms
  84 bytes from 10.5.10.254 icmp_seq=4 ttl=255 time=6.261 ms
  84 bytes from 10.5.10.254 icmp_seq=5 ttl=255 time=4.203 ms

* admin2> ping 10.5.10.254
  84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=8.590 ms
  84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=4.564 ms
  84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=9.321 ms
  84 bytes from 10.5.10.254 icmp_seq=4 ttl=255 time=12.958 ms
  84 bytes from 10.5.10.254 icmp_seq=5 ttl=255 time=4.413 ms

* admin3> ping 10.5.10.254
  84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=9.727 ms
  84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=9.450 ms
  84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=6.069 ms
  84 bytes from 10.5.10.254 icmp_seq=4 ttl=255 time=5.355 ms
  84 bytes from 10.5.10.254 icmp_seq=5 ttl=255 time=5.888 ms

* guest3> ping 10.5.20.254
  84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=9.926 ms
  84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=3.739 ms
  84 bytes from 10.5.20.254 icmp_seq=3 ttl=255 time=6.489 ms
  84 bytes from 10.5.20.254 icmp_seq=4 ttl=255 time=7.445 ms
  84 bytes from 10.5.20.254 icmp_seq=5 ttl=255 time=6.099 ms

- [admin3>] ping 10.5.10.254
  84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=9.651 ms
  84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=4.455 ms
  84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=8.231 ms
  84 bytes from 10.5.10.254 icmp_seq=4 ttl=255 time=9.521 ms
  84 bytes from 10.5.10.254 icmp_seq=5 ttl=255 time=13.511 ms

* guest1> ping 10.5.20.254
  84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=8.972 ms
  84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=5.524 ms
  84 bytes from 10.5.20.254 icmp_seq=3 ttl=255 time=8.387 ms
  84 bytes from 10.5.20.254 icmp_seq=4 ttl=255 time=9.761 ms
  84 bytes from 10.5.20.254 icmp_seq=5 ttl=255 time=12.590 ms

## 🌞 Configurer un source NAT sur le routeur (comme au TP4)

Interfaces "externes" :
(config)# interface fastEthernet 0/0
(config-if)# ip nat outside

(config)#interface fastethernet 0/0
(config-if)#ip address dhcp

Interfaces "internes" :
(config)# interface fastEthernet 1/0.10 (.20 plus tard)
(config-if)# ip nat inside

(config)# access-list 1 permit any

(config)# ip nat inside source list 1 interface fastEthernet 0/0 overload

## 🌞 Vérifier que les clients et les admins peuvent joindre Internet

- [admin3>] ping 8.8.8.8
  84 bytes from 8.8.8.8 icmp_seq=1 ttl=51 time=74.500 ms
  84 bytes from 8.8.8.8 icmp_seq=2 ttl=51 time=24.302 ms
  84 bytes from 8.8.8.8 icmp_seq=3 ttl=51 time=40.425 ms
  84 bytes from 8.8.8.8 icmp_seq=4 ttl=51 time=24.952 ms
  84 bytes from 8.8.8.8 icmp_seq=5 ttl=51 time=33.208 ms

* admin2> ping 8.8.8.8
  84 bytes from 8.8.8.8 icmp_seq=1 ttl=51 time=39.483 ms
  84 bytes from 8.8.8.8 icmp_seq=2 ttl=51 time=23.372 ms
  84 bytes from 8.8.8.8 icmp_seq=3 ttl=51 time=34.849 ms
  84 bytes from 8.8.8.8 icmp_seq=4 ttl=51 time=26.078 ms
  84 bytes from 8.8.8.8 icmp_seq=5 ttl=51 time=23.941 ms

* admin3> ping 8.8.8.8
  84 bytes from 8.8.8.8 icmp_seq=1 ttl=51 time=30.373 ms
  84 bytes from 8.8.8.8 icmp_seq=2 ttl=51 time=34.255 ms
  84 bytes from 8.8.8.8 icmp_seq=3 ttl=51 time=35.843 ms
  84 bytes from 8.8.8.8 icmp_seq=4 ttl=51 time=27.199 ms
  84 bytes from 8.8.8.8 icmp_seq=5 ttl=51 time=41.959 ms

- guest1> ping 8.8.8.8
  84 bytes from 8.8.8.8 icmp_seq=1 ttl=51 time=30.073 ms
  84 bytes from 8.8.8.8 icmp_seq=2 ttl=51 time=37.486 ms
  84 bytes from 8.8.8.8 icmp_seq=3 ttl=51 time=25.195 ms
  84 bytes from 8.8.8.8 icmp_seq=4 ttl=51 time=34.001 ms
  84 bytes from 8.8.8.8 icmp_seq=5 ttl=51 time=32.531 ms

* guest2> ping 8.8.8.8
  84 bytes from 8.8.8.8 icmp_seq=1 ttl=51 time=25.083 ms
  84 bytes from 8.8.8.8 icmp_seq=2 ttl=51 time=31.136 ms
  84 bytes from 8.8.8.8 icmp_seq=3 ttl=51 time=22.926 ms
  84 bytes from 8.8.8.8 icmp_seq=4 ttl=51 time=22.142 ms
  84 bytes from 8.8.8.8 icmp_seq=5 ttl=51 time=23.502 ms

* guest3> ping 8.8.8.8
  84 bytes from 8.8.8.8 icmp_seq=1 ttl=51 time=29.951 ms
  84 bytes from 8.8.8.8 icmp_seq=2 ttl=51 time=42.513 ms
  84 bytes from 8.8.8.8 icmp_seq=3 ttl=51 time=35.978 ms
  84 bytes from 8.8.8.8 icmp_seq=4 ttl=51 time=24.019 ms
  84 bytes from 8.8.8.8 icmp_seq=5 ttl=51 time=27.551 ms

## 🌞 Vérifier et prouver qu'un client branché à client-sw3 peut récupérer une IP dynamiquement.

### serv dhcp (j'ai changé les bails comme je pensais que ça passait 🤠)

DHCP Server Configuration file.

see /usr/share/doc/dhcp\*/dhcpd.conf.example see dhcpd.conf(5) man page

DHCP lease lifecycle

default-lease-time 600; max-lease-time 7200;

This server is the only DHCP server we got So it is the authoritative one

authoritative;

Configure logging

log-facility local7;

Actually configure the DHCP server to serve our network

subnet 10.5.20.0 netmask 255.255.255.0 {

IPs that our DHCP server can give to client

range 10.5.20.100 10.5.20.150;

Domain name served and DNS server (optional) The DHCP server gives this info to clients

option domain-name "tp5-b1";
option domain-name-servers 1.1.1.1;

Gateway of the network (optional) The DHCP server gives this info to clients

option routers 10.5.20.254;

Specify broadcast addres of the network (optional)

option broadcast-address 10.5.20.255;
}

## 🌞 Vérifier et prouver qu'un client branché à client-sw3 peut récupérer une IP dynamiquement.

- guest2> ip dhcp
  DDORA IP 10.5.20.100/24 GW 10.5.20.254

AH! oOpS c'était pas le bon switch 🙃

- guest3> ip dhcp
  DDORA IP 10.5.20.101/24 GW 10.5.20.254

## 🌞 Tester que le serveur Web fonctionne

### depuis le serveur web lui-même : curl localhost:80

[root@web ~]# curl localhost:80

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
<head>
  <title>Welcome to CentOS</title>
  <style rel="stylesheet" type="text/css">

        html {
        background-image:url(img/html-background.png);
        background-color: white;
        font-family: "DejaVu Sans", "Liberation Sans", sans-serif;
        font-size: 0.85em;
        line-height: 1.25em;
        margin: 0 4% 0 4%;
        }

        body {
        border: 10px solid #fff;
        margin:0;
        padding:0;
        background: #fff;
        }

        /* Links */

        a:link { border-bottom: 1px dotted #ccc; text-decoration: none; color: #204d92; }
        a:hover { border-bottom:1px dotted #ccc; text-decoration: underline; color: green; }
        a:active {  border-bottom:1px dotted #ccc; text-decoration: underline; color: #204d92; }
        a:visited { border-bottom:1px dotted #ccc; text-decoration: none; color: #204d92; }
        a:visited:hover { border-bottom:1px dotted #ccc; text-decoration: underline; color: green; }

        .logo a:link,
        .logo a:hover,
        .logo a:visited { border-bottom: none; }

        .mainlinks a:link { border-bottom: 1px dotted #ddd; text-decoration: none; color: #eee; }
        .mainlinks a:hover { border-bottom:1px dotted #ddd; text-decoration: underline; color: white; }
        .mainlinks a:active { border-bottom:1px dotted #ddd; text-decoration: underline; color: white; }
        .mainlinks a:visited { border-bottom:1px dotted #ddd; text-decoration: none; color: white; }
        .mainlinks a:visited:hover { border-bottom:1px dotted #ddd; text-decoration: underline; color: white; }

        /* User interface styles */

        #header {
        margin:0;
        padding: 0.5em;
        background: #204D8C url(img/header-background.png);
        text-align: left;
        }

        .logo {
        padding: 0;
        /* For text only logo */
        font-size: 1.4em;
        line-height: 1em;
        font-weight: bold;
        }

        .logo img {
        vertical-align: middle;
        padding-right: 1em;
        }

        .logo a {
        color: #fff;
        text-decoration: none;
        }

        p {
        line-height:1.5em;
        }

        h1 {
                margin-bottom: 0;
                line-height: 1.9em; }
        h2 {
                margin-top: 0;
                line-height: 1.7em; }

        #content {
        clear:both;
        padding-left: 30px;
        padding-right: 30px;
        padding-bottom: 30px;
        border-bottom: 5px solid #eee;
        }

    .mainlinks {
        float: right;
        margin-top: 0.5em;
        text-align: right;
    }

    ul.mainlinks > li {
    border-right: 1px dotted #ddd;
    padding-right: 10px;
    padding-left: 10px;
    display: inline;
    list-style: none;
    }

    ul.mainlinks > li.last,
    ul.mainlinks > li.first {
    border-right: none;
    }

  </style>

</head>

<body>

<div id="header">

    <ul class="mainlinks">
        <li> <a href="http://www.centos.org/">Home</a> </li>
        <li> <a href="http://wiki.centos.org/">Wiki</a> </li>
        <li> <a href="http://wiki.centos.org/GettingHelp/ListInfo">Mailing Lists</a></li>
        <li> <a href="http://www.centos.org/download/mirrors/">Mirror List</a></li>
        <li> <a href="http://wiki.centos.org/irc">IRC</a></li>
        <li> <a href="https://www.centos.org/forums/">Forums</a></li>
        <li> <a href="http://bugs.centos.org/">Bugs</a> </li>
        <li class="last"> <a href="http://wiki.centos.org/Donate">Donate</a></li>
    </ul>

        <div class="logo">
                <a href="http://www.centos.org/"><img src="img/centos-logo.png" border="0"></a>
        </div>

</div>

<div id="content">

        <h1>Welcome to CentOS</h1>

        <h2>The Community ENTerprise Operating System</h2>

        <p><a href="http://www.centos.org/">CentOS</a> is an Enterprise-class Linux Distribution derived from sources freely provided

to the public by Red Hat, Inc. for Red Hat Enterprise Linux. CentOS conforms fully with the upstream vendors
redistribution policy and aims to be functionally compatible. (CentOS mainly changes packages to remove upstream vendor
branding and artwork.)</p>

        <p>CentOS is developed by a small but growing team of core

developers.&nbsp; In turn the core developers are supported by an active user community
including system administrators, network administrators, enterprise users, managers, core Linux contributors and Linux enthusiasts from around the world.</p>

        <p>CentOS has numerous advantages including: an active and growing user community, quickly rebuilt, tested, and QA'ed errata packages, an extensive <a href="http://www.centos.org/download/mirrors/">mirror network</a>, developers who are contactable and responsive, Special Interest Groups (<a href="http://wiki.centos.org/SpecialInterestGroup/">SIGs</a>) to add functionality to the core CentOS distribution, and multiple community support avenues including a <a href="http://wiki.centos.org/">wiki</a>, <a

href="http://wiki.centos.org/irc">IRC Chat</a>, <a href="http://wiki.centos.org/GettingHelp/ListInfo">Email Lists</a>, <a href="https://www.centos.org/forums/">Forums</a>, <a href="http://bugs.centos.org/">Bugs Database</a>, and an <a
href="http://wiki.centos.org/FAQ/">FAQ</a>.</p>

        </div>

</div>

</body>
</html>

### depuis une autre machine du réseau : curl 10.5.30.12:80-------------------------------------------------------

[root@dhcp ~]# curl 10.5.30.12:80

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
<head>
  <title>Welcome to CentOS</title>
  <style rel="stylesheet" type="text/css">

        html {
        background-image:url(img/html-background.png);
        background-color: white;
        font-family: "DejaVu Sans", "Liberation Sans", sans-serif;
        font-size: 0.85em;
        line-height: 1.25em;
        margin: 0 4% 0 4%;
        }

        body {
        border: 10px solid #fff;
        margin:0;
        padding:0;
        background: #fff;
        }

        /* Links */

        a:link { border-bottom: 1px dotted #ccc; text-decoration: none; color: #204d92; }
        a:hover { border-bottom:1px dotted #ccc; text-decoration: underline; color: green; }
        a:active {  border-bottom:1px dotted #ccc; text-decoration: underline; color: #204d92; }
        a:visited { border-bottom:1px dotted #ccc; text-decoration: none; color: #204d92; }
        a:visited:hover { border-bottom:1px dotted #ccc; text-decoration: underline; color: green; }

        .logo a:link,
        .logo a:hover,
        .logo a:visited { border-bottom: none; }

        .mainlinks a:link { border-bottom: 1px dotted #ddd; text-decoration: none; color: #eee; }
        .mainlinks a:hover { border-bottom:1px dotted #ddd; text-decoration: underline; color: white; }
        .mainlinks a:active { border-bottom:1px dotted #ddd; text-decoration: underline; color: white; }
        .mainlinks a:visited { border-bottom:1px dotted #ddd; text-decoration: none; color: white; }
        .mainlinks a:visited:hover { border-bottom:1px dotted #ddd; text-decoration: underline; color: white; }

        /* User interface styles */

        #header {
        margin:0;
        padding: 0.5em;
        background: #204D8C url(img/header-background.png);
        text-align: left;
        }

        .logo {
        padding: 0;
        /* For text only logo */
        font-size: 1.4em;
        line-height: 1em;
        font-weight: bold;
        }

        .logo img {
        vertical-align: middle;
        padding-right: 1em;
        }

        .logo a {
        color: #fff;
        text-decoration: none;
        }

        p {
        line-height:1.5em;
        }

        h1 {
                margin-bottom: 0;
                line-height: 1.9em; }
        h2 {
                margin-top: 0;
                line-height: 1.7em; }

        #content {
        clear:both;
        padding-left: 30px;
        padding-right: 30px;
        padding-bottom: 30px;
        border-bottom: 5px solid #eee;
        }

    .mainlinks {
        float: right;
        margin-top: 0.5em;
        text-align: right;
    }

    ul.mainlinks > li {
    border-right: 1px dotted #ddd;
    padding-right: 10px;
    padding-left: 10px;
    display: inline;
    list-style: none;
    }

    ul.mainlinks > li.last,
    ul.mainlinks > li.first {
    border-right: none;
    }

  </style>

</head>

<body>

<div id="header">

    <ul class="mainlinks">
        <li> <a href="http://www.centos.org/">Home</a> </li>
        <li> <a href="http://wiki.centos.org/">Wiki</a> </li>
        <li> <a href="http://wiki.centos.org/GettingHelp/ListInfo">Mailing Lists</a></li>
        <li> <a href="http://www.centos.org/download/mirrors/">Mirror List</a></li>
        <li> <a href="http://wiki.centos.org/irc">IRC</a></li>
        <li> <a href="https://www.centos.org/forums/">Forums</a></li>
        <li> <a href="http://bugs.centos.org/">Bugs</a> </li>
        <li class="last"> <a href="http://wiki.centos.org/Donate">Donate</a></li>
    </ul>

        <div class="logo">
                <a href="http://www.centos.org/"><img src="img/centos-logo.png" border="0"></a>
        </div>

</div>

<div id="content">

        <h1>Welcome to CentOS</h1>

        <h2>The Community ENTerprise Operating System</h2>

        <p><a href="http://www.centos.org/">CentOS</a> is an Enterprise-class Linux Distribution derived from sources freely provided

to the public by Red Hat, Inc. for Red Hat Enterprise Linux. CentOS conforms fully with the upstream vendors
redistribution policy and aims to be functionally compatible. (CentOS mainly changes packages to remove upstream vendor
branding and artwork.)</p>

        <p>CentOS is developed by a small but growing team of core

developers.&nbsp; In turn the core developers are supported by an active user community
including system administrators, network administrators, enterprise users, managers, core Linux contributors and Linux enthusiasts from around the world.</p>

        <p>CentOS has numerous advantages including: an active and growing user community, quickly rebuilt, tested, and QA'ed errata packages, an extensive <a href="http://www.centos.org/download/mirrors/">mirror network</a>, developers who are contactable and responsive, Special Interest Groups (<a href="http://wiki.centos.org/SpecialInterestGroup/">SIGs</a>) to add functionality to the core CentOS distribution, and multiple community support avenues including a <a href="http://wiki.centos.org/">wiki</a>, <a

href="http://wiki.centos.org/irc">IRC Chat</a>, <a href="http://wiki.centos.org/GettingHelp/ListInfo">Email Lists</a>, <a href="https://www.centos.org/forums/">Forums</a>, <a href="http://bugs.centos.org/">Bugs Database</a>, and an <a
href="http://wiki.centos.org/FAQ/">FAQ</a>.</p>

        </div>

</div>

</body>
</html>

# A partir de là j'ai continué comme je pouvais j'ai pas réussi à faire démarrer le serv dns. 😶

## 🌞 Ouvrir le port firewall lié au DHCP

### utilisez la commande ss pour trouver quel(s) port(s) vous devez ouvrir

[root@dns ~]# ss -lnutp
Netid State Recv-Q Send-Q Local Address:Port Peer Address:Port udp UNCONN 0 0 _:68 _:\* users:(("dhclient",pid=872,fd=6))

### ouvrez le port dans le firewall

### On ouvre le port 68 en udp.

firewall-cmd --add-port=68/udp --permanent

## 🌞 Ajouter l'option DHCP liée au DHCP

### modifier la configuration de votre serveur DHCP pour qu'ils fournissent les options suivantes :

adresse de la passerelle
adresse du serveur DNS de l'infra

DHCP Server Configuration file.

see /usr/share/doc/dhcp\*/dhcpd.conf.example see dhcpd.conf(5) man page

DHCP lease lifecycle

default-lease-time 600; max-lease-time 7200;

This server is the only DHCP server we got So it is the authoritative one

authoritative;

Configure logging

log-facility local7;

Actually configure the DHCP server to serve our network

subnet 10.5.20.0 netmask 255.255.255.0 {

IPs that our DHCP server can give to client

range 10.5.20.100 10.5.20.150;

Domain name served and DNS server (optional) The DHCP server gives this info to clients

option domain-name "tp5-b1";
option domain-name-servers 10.5.30.11;

Gateway of the network (optional) The DHCP server gives this info to clients

option routers 10.5.20.254;

Specify broadcast addres of the network (optional)

option broadcast-address 10.5.20.255;
