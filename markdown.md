# This is a heading
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5

---

**Bold**

__BOLD__

---

*italic*

_Italic_

---

***BOLD and italic***

**_BOLD and italic_**

__*BOLD and italic*__

___BOLD and Italic___


---
~~strike through~~

---
> This is a quote 
---

* -

- bullet
    - sub bullet
        - sub sub bullet
            - sub sub sub bullet

* bullet
    * sub bullet
        * sub sub bullet
            * sub sub sub bullet

---
1. one
2. two

---
1. online - [elastic](https://elastic.io)
2. local link - [Readme](../Users/dvluong/NSM-Engineering/README.md)

---
`systemctl start kibana`

```
suricata.yaml
```
---

| Column1 | Column2 | Column3 | Column4 | 
| --- | --- | --- | --- |
| Data1 | Data2 | Data3 | Data4

---

<!--you cant see this -->

---
# Date 1 Notes
---

## Lab Topology 
<img src="network.png">

- Edge router
    - uplink
    - repo server

---

## Suricata Install

1. Install
suricata
`sudo yum install suricata`
2. edit yaml 
``` 
kjkj
 ```
---
 Turning on Pfsense
 1. plug in usb(marked pfsense)
 2. turn on pfsense box and press F11 on keyobard
 3. press 5 to reboot and keep pressing f5 in order to boot up to usb

 6. configure lan 172.16.10.1
 7. type 24.. for mask 255.255.255.0
 8. enable DHCP server on LAN (y)...starting range: 172.16.10.100
 9. ending range: 172.16.10.254

pfsense: user and password: admin and pfsense 



11. firewall: Wan
uncheck the last two


12. Services: DNS Forwarder
uncheck: enable DNS forwarder
and save

13. Diagnostic/Halt Systems
click on halt and ok

---
# Date 2 Notes 

# troubleshooting
1. student laptop to edgerouter: 192.168.2.1 ( ping to see if the router is working)

2. Switch interface to downstream Cisco switch (10.0.0.2)

3. 10.0.0.1 cisco switch port 24 to edge router

4. 10.0.10.1 physical port on switch (group number) 

5. 172.16.10.1 to pfsense

6. 172.16.10.100 --> sensor 


remove vga cable from monitor, make sure hdmi is connected to montior from nuc 
1. insert usb (centos) and press f10 when power on NUC
2. highlighted white on part 1: os bootloader and press enter
3. test this media and install centos 7

eno1: management 
looking at the nuc on the back.. management is on the right and connect it to lan2 port on pfsense
switch the tab on from off

configure >> ipv4>> method: manual >> address: 172.16.10.100 netmask: 255.255.255.0 gateway: 172.16.10.1

ipv6: method ignore 
bottom left: sg10.local.lan 

then go to date and time: region: etc. city: coordinate univseral time
make sure network time is set to on

software selection: minimal install
kdump: uncheck enable kdump


installation destination 
click on both of them and blow it away--auto configure partitioning and check I would like to make additional space availble
click done...delete all and then reclaim space

click installation destination again 
check both disk .. and then I will configure partitioning 

click here to create them auto..
/home (1GB)
/ (1GB, volume group: vg_os. and selected EVO 500GB

bottom left: plus sign: /var/log, desired cap: 1 Gb
/tmp, 1 gb
/var, 1 gb
/var/log/audit, 1 gb
/var/tmp

swap

/data/stenographer 
/data/suricata
/data/kafka 
/data/zeek
/data/fsf
/data/elasticsearch


update at the bottom right
change all the VG for all that does not have data..to vg_os


now with all data partition: make sure it's "vg_data" and 1 TB

change the capacity:
/home (50G)
/ (blank, 

bottom left: plus sign: 
/var/log, desired cap: 50 g
/tmp, 5 g
/var, 50 g
/var/log/audit, 50 g
/var/tmp10 g

swap 4 g

/data/stenographer 500 g
/data/suricata 25 g
/data/kafka 100 g
/data/zeek 25 g
/data/fsf 10 g 
/data/elasticsearch blank 
done and accept changes

begin installation
admin, and pw: password

sudo vi /etc/sysconfig/network-scripts/ifcfg-eno1
vi and change all IPV6 to "no"
and make sure onboot is "yes"

sudo vi /etc/sysctl.conf
vi: net.ipv6.conf.all.disable_ipv6=1
    net.ipv6.conf.default.disable_ipv6=1

sudo vi /etc/hosts
comment out second line

sudo sysctl -p (force changes)
This complete sensor install

---

Point to repository

1. cd /etc/yum.repo.d ------ ls -s
2. remove the .repos --- sudo rm -r CentOS-*
3. go to 192.168.2.20:8080 and copy the addr
4. go to terminal and sudo curl -LO http://192.168.2.20:8080/local.repo
5. cat local.repo 

6. sudo yum makecache (creating cache for the repo)
7. sudo yum list zeek
8. sudo yum update (update on packages sensor) and type y to download

9. sudo systemctl reboot
10. connect again ssh admin@172.16.10.100
cd /etc/yum.repos.d/
sudo yum makecahe --disablerepo="*" --enablerepo=local*

sudo yum update --disablerepo=*" --enablerepo=local*

sudo systemctl reboot

SELinux
(Security Enhanced Linux)
MAC (Mandatory access control) and DAC (discretionary)
Mode:Permissive, enforcing, and disabled

sestatus (just to see SElinux (keep enabled)

