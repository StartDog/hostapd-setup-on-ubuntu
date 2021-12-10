The script below and two conf filesare needed - this is 7 hours of work!  Maybe you can eliminate something
from hostapd.conf or change it 

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

# of course you change all the interface names to your names. 



