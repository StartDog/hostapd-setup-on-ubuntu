The script below and two conf files are needed - this is 7 hours of work!  Maybe you can eliminate something
from hostapd.conf or change it.  Changing it to work with IPV6 would be another bit of work.  You can change the hw_mode or the channel to suit your hardware. Country code too. 

# hostapd-setup-on-ubuntu
a script and changes needed to run hostapd on ubuntu

below is the /etc/hostapd/hostapd.conf file"

#cat /etc/hostapd/hostapd.conf 

# hostapd.conf
ssid=test-wifi-name

hw_mode=g

channel=6

beacon_int=500

country_code=US

wpa=2

wpa_passphrase=mypasswordwhaeverthatis

wpa_key_mgmt=WPA-PSK

wpa_group_rekey=86400

#wpa_pairwise=TKIP

#skip_inactivity_poll=1

own_ip_addr=192.168.5.1

#wpa_strict_rekey=1

#disassoc_low_ack=0

internet=1

#

Below is the script to actually run hostapd.  I just call it runhost.sh

# cat runhost.sh

# runtest.sh

echo "start up wifi ap"

echo "set ap power to auto"

/sbin/iwconfig wlx1cbfce847aaf txpower auto

#/sbin/iwconfig wlx1cbfce847aaf txpower 100mW

echo "make sure iptables are changed"

/sbin/iptables -t nat -A POSTROUTING -o enp4s0 -j MASQUERADE

/sbin/iptables -A FORWARD -i wlx1cbfce847aaf -o enp4s0 -m state --state RELATED,ESTABLISHED -j ACCEPT

/sbin/iptables -A FORWARD -i wlx1cbfce847aaf -o enp4s0 -j ACCEPT

echo "start hostapd"

/usr/sbin/hostapd  -i wlx1cbfce847aaf /etc/hostapd/hostapd.conf

#---------------------------------------------------#
one further change is required - changes required in /etc/sysctl.conf  - plus a reboot

# /etc/sysctl.conf changes - require a reboot

# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1
# Uncomment the next line to enable packet forwarding for IPv6
#  Enabling this option disables Stateless Address Autoconfiguration
#  based on Router Advertisements for this host
net.ipv6.conf.all.forwarding=1

# further network related changes:

# of course you change all the interface names  and IPs to your names. 

# set up of the network interfaces.
The important one is the wlx... device.  That is the wifi you need to give it an IP - I gave it 192.168.5.1  and I manually set up and IP on the addresses on the connecting device - say 192.168.5.2.  I guess you could use DHCP but that makes it too easy. I have a DNS server I added myself - add yours or your ISPs   Ignore the  159 address except use your eth0 or whatever interface - the 100 below is your gateway probably 3 lines below could be eliminated (flush up down)

/sbin/ifconfig wlx1cbfce847aaf down

/sbin/ifconfig enp4s0 inet 159.134.205.122 netmask 255.255.255.0 

/sbin/ifconfig wlx1cbfce847aaf inet 192.168.5.1 netmask 255.255.255.0

/sbin/ip route flush 159.134.205.122

/sbin/ip route add 159.134.205.0/24 dev enp4s0 

/sbin/route add default gw 159.134.205.100

/sbin/ifconfig wlx1cbfce847aaf up




