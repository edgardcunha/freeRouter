
hostname r1
!
crypto ipsec ips1
 description <r1-private-key><wireguard-server-public-key>
 key GExWuXQ9RV4E+xeK/sXjk+DvBPJEAVbS5Z0KaWh2fV0=8i7QSghwslgQ3LCHg5pnEtkVgyd1KooDJK5F3xKYJTc=
 exit
!
prefix-list all4
 sequence 10 permit 0.0.0.0/0 ge 0 le 0
 exit
!
prefix-list all6
 sequence 10 permit ::/0 ge 0 le 0
 exit
!
vrf def v1
 exit
!
int eth1
 no desc
 vrf for v1
 ipv4 address dynamic dynamic
 ipv4 gateway-prefix all4
 ipv4 dhcp-client enable
 ipv4 dhcp-client early
 no shutdown
 no log-link-change
 exit
!
interface tunnel1
 description r2@eth1 -> wireguard-server[200.137.68.76]@51820
 tunnel key 51820
 tunnel vrf v1
 tunnel protection ips1
 tunnel source eth1
 tunnel destination 200.137.68.76
 tunnel mode wireguard
 vrf forwarding v1
 ipv4 address 10.250.250.244 /32
 no shutdown
 no log-link-change
 exit
!
server telnet tel
 security protocol telnet
 exec timeout 10000000
 exec logging
 no exec authorization
 no login authentication
 login logging
 vrf v1
 exit
!
client udp-checksum transmit
ipv4 route v1 10.250.250.244 /32 10.0.2.16
end
