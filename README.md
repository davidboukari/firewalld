# firewalld

## Centos
```
yum install  yum install iptables-services
cat  /etc/sysconfig/iptables

$ firewall-cmd --state
running

firewall-cmd --get-default-zone

firewall-cmd --get-active-zone

firewall-cmd --list-all

firewall-cmd --get-zones
block dmz drop external home internal public trusted work

firewall-cmd --zone=home --list-all
home
  target: default
  icmp-block-inversion: no
  interfaces:
  sources:
  services: dhcpv6-client mdns samba-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

firewall-cmd --list-all-zones|less

firewall-cmd --zone=home --list-all
home
  target: default
  icmp-block-inversion: no
  interfaces:
  sources:
  services: dhcpv6-client mdns samba-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

firewall-cmd --zone=home --change-interface=eth0
success

firewall-cmd --get-active-zones
home
  interfaces: eth0


firewall-cmd --set-default-zone=home

firewall-cmd --get-services
RH-Satellite-6 RH-Satellite-6-capsule amanda-client ...

ls /usr/lib/firewalld/
helpers  icmptypes  ipsets  services  zones

ls /usr/lib/firewalld/services
cat ssh.xml
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>SSH</short>
  <description>Secure Shell (SSH) is a protocol for logging into and executing commands on remote machines. It provides secure encrypted communications. If you plan on accessing your machine remotely via SSH over a firewalled interface, enable this option. You need the openssh-server package installed for this option to be useful.</description>
  <port protocol="tcp" port="22"/>
</service>

firewall-cmd --zone=public --add-service=http
success

firewall-cmd --zone=public --list-services
dhcpv6-client http ssh

firewall-cmd  --zone=public --add-service=http --permanent
success

firewall-cmd --zone=public --list-services --permanent
dhcpv6-client http ssh

firewall-cmd --zone=public --add-service=https
success
$ firewall-cmd --zone=public --list-services
dhcpv6-client http https ssh
$ firewall-cmd --zone=public --list-services  --permanent
dhcpv6-client http ssh

# Add port
firewall-cmd --zone=public --add-port=5000/tcp
success
firewall-cmd --zone=public --list-ports
5000/tcp

# Add custom service
cp /usr/lib/firewalld/services/ssh.xml /usr/lib/firewalld/services/example.xml

cat cat /usr/lib/firewalld/services/example.xml
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>Example Service</short>
  <description>
This is just an example service
</description>
  <port protocol="tcp" port="7777"/>
  <port protocol="tcp" port="8888"/>
</service>

firewall-cmd --reload
success
firewall-cmd --zone=public --add-service=example
success


# Create customs zones
firewall-cmd --permanent --new-zone=publicweb
success
firewall-cmd --permanent --new-zone=privateDNS
success

# Needs to reload because new zone does not exist
firewall-cmd --get-zones
block dmz drop external home internal public trusted work

firewall-cmd --get-zones --permanent
block dmz drop external home internal privateDNS public publicweb trusted work

firewall-cmd --reload

firewall-cmd --get-zones
block dmz drop external home internal privateDNS public publicweb trusted work


## Add service in the custom zone
### Web zone
firewall-cmd --get-zones
block dmz drop external home internal privateDNS public publicweb trusted work
[root@localhost centos]# firewall-cmd --zone=publicweb --add-service=ssh --permanent
success
[root@localhost centos]# firewall-cmd --zone=publicweb --add-service=http --permanent
success
[root@localhost centos]# firewall-cmd --zone=publicweb --add-service=https --permanent
success
firewall-cmd --reload
firewall-cmd --zone=publicweb --list-all
publicweb
  target: default
  icmp-block-inversion: no
  interfaces:
  sources:
  services: http https ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

firewall-cmd --zone=privateDNS --^Cd-service=dns --permanent
firewall-cmd --zone=publicweb --add-interface=eth0 --permanent
The interface is under control of NetworkManager, setting zone to 'publicweb'.
success
firewall-cmd --zone=publicweb --list-interfaces
eth0

### DNS zone
firewall-cmd --zone=privateDNS --add-service=dns --permanent
firewall-cmd --reload
firewall-cmd --zone=privateDNS --list-all
privateDNS
  target: default
  icmp-block-inversion: no
  interfaces:
  sources:
  services: dns
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:


firewall-cmd --zone=publicweb --change-interface=eth1 --permanent
```
