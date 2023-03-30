# Day 3 Notes

ip a 
setting up sniffing traffic
setting up enp5s0

disabling Nic setting
sudo ethtool -k enp5s0
(rx_checksumming and tx-checksumming: both are on) 

1. cd (go back to ~) and then Curl -LO http://192.168.2.20:8080/interface_prep.sh
2. ll
3. cat interface_prep.sh
4. sudo chmod +x interface_prep.sh (to make it executeable)
5. sudo ./interface_prep.sh enp5s0
6. sudo ethtool -k enp5s0 
7. cd /sbin
curl -LO http://192.168.2.20:8080/ifup-local  since it failed writing body

sudo !! and enter again

cat ifup-local...(for putting enp5so)
sudo chmod +x ifup-local


cd /etc/sysconfig/network-scripts/ifup (not a directory so...) >> cd /etc/sysconfig/network-scripts/ >> ll

sudo vi ifup
insert >>> toward the end:

if [ -x /sbin/ifup-local ]; then 
    /sbin/ifup-local ${DEVICE}
fi

network script for enp50(make sure you in network script dir)
sudo vi ifcfg-enp5s0
change BOOTPROTO to none as well as DEFROUTE to none
change IPV6_AUTOCONF=no and IPV6_DEFROUTE=no
change ONBOOT to yes and add NM_CONTROLLED=no
hit escape and : set nu (for numbering)

sudo systemctl reboot
sudo ethtool -k enp5s0 and check RX and TX checksum is off
ip a: you can see enp5s0 is in promisc mode

---

connecting the TAP and verifying traffic is flowing through it
3 connections:
1. Looking at Back NUC connect (right side: CAPTURE) to monitoring TAP
2. management cable from NUC go to TAP port 1
3. port 2 tap connect to pf sense LAN2 

cd /etc/yum.repos.d
sudo yum install tcpdump --disablerepo=* --enablerepo=local*

sudo tcpdump -i enp5s0
sudo tcpdump -nn -i enp5s0 -c4

sudo tcpdump -nn -i enp5s0 '!port 22'





to remove centos run the below command:
sudo rm -rf centos-*

sudo yum install stenographer

cd /etc/stenographer
modified config file

sudo vi config
update data path(look at the path location:sudo which stenotype)

{
    "Threads": [
        { "PacketsDirectory: "/data/stenographer/packets", "IndexDirectory": "/data/stenographer/index", "MaxDirectoryFiles": 30000,
         "DiskFreePercentage": 10
         }
    ]
, "StenotypePath": "/bin/stenotype"
, "Interface": "enp5s0"
, "Port": 1234
, "Host": "127.0.0.1"
, "Flags": []
, "CertPath": "/etc/stenographer/certs"

}

sudo mkdir /data/stenographer/packets
sudo mkdir /data/stenogrpaher/index
cd /data/stenographer and enter pwd
type: ll to list content
sudo chown -R stenogrpaher:stenogrpaher /data/stenographer and enter: 

sudo stenokeys.sh stenographer stenographer

cd /etc/stenographer/certs
ll

start stenographer
sudo systemctl start stenographer
sudo systemctl status stenographer

^status^start

sudo systemctl enable stenographer

sudo journalctl -xeu stenographer (troubleshooting by looking at the log)


to show that stenographer is running.. cd /data/stenographer/packets
watch ls -al

---


installing suricata
sudo yum list suricata
sudo yum install suricata and wait and then type y

cd /etc/suricata
sudo -s
cd /etc/suricata
ll

sudo vi suricata.yaml (setting up network)
:set nu for numbering
go to line 56 and change it to: /data/suricata
line 60 change enabled from yes to no 
line 76: enabled from yes to no 
line 404; change enabled from yes to no
line 557: enabled from yes to no 
line 580: changed the interface to: enp5s0
line 582: uncomment thread: auto
****(make sure the syntax line up)
:wq

cd /etc/sysconfig/suricata
cd /etc/sysconfig/

sudo vi /etc/sysconfig/suricata

update to OPTIONS="--af-packet=enp5s0 --user suricata "

sudo suricata-update add-source local-emerging-threats http://192.168.2.20:8080/emerging.rules.tar

sudo suricata-update

sudo suricata-update list-sources 


cd /data
ll
sudo chown -R suricata: /data/suricata
systemctl start suricata
^start^enable
^enable^status
systemctl status suricata
curl -LO http://192.168.2.20:8080/all-class-files.zip

cd cd /data/suricata
ll
cat eve.json | jq
tail -f eve.json
