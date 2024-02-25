**Setting Up Startlink And Fortigate IPv6 2024-02-25 Fortigate 7.2.7 Firmware**

Dishy v1 Round Testing and working

Starlink ------> POE (OEM) -------> WAN2 Fortigate 

No Starlink Router!! I am using SD-WAN. WAN1 doesn't support IPv6 so just WAN2 is for failover and primary IPv6.

WAN2 settings:

    config system interface
    edit wan2
    config ipv6
    config ipv6
        set ip6-allowaccess ping
        set dhcp6-prefix-delegation enable
        set autoconf enable
        config dhcp6-iapd-list
            edit 1
                set prefix-hint ::/56
            next
        end
    end

Internal Settings:

    config system interface
    edit "internal"
    config ipv6
        set ip6-mode delegated
        set ip6-allowaccess ping https ssh
        set dhcp6-prefix-delegation enable
        set ip6-send-adv enable
        set ip6-manage-flag enable
        set ip6-other-flag enable
        set ip6-delegated-prefix-iaid 1
        set ip6-upstream-interface "wan2"
        set ip6-subnet ::1/64
        config ip6-delegated-prefix-list
            edit 1
                set upstream-interface "wan2"
                set delegated-prefix-iaid 1
                set subnet ::/64
                set rdnss-service default
            next
        end
        config dhcp6-iapd-list
            edit 1
                set prefix-hint ::/56
            next
        end
    end

Rules. PLEASE NOTE rule numbers may very, don't just copy and paste this. Also note that my interface is virtual-wan-link, unless you are using SD-WAN you will need to change this.

    config firewall policy
    edit 31
        set name "Allow_ping"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "ALL_ICMP6"
        set inspection-mode proxy
        next
    edit 32
        set name "ALLOW_HTTP"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "HTTP"
        set utm-status enable
        set inspection-mode proxy
        set ssl-ssh-profile "certificate-inspection"
        set av-profile "default"
        set dnsfilter-profile "default"
        set application-list "default"
    next
    edit 33
        set name "ALLOW_HTTPS"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "HTTPS"
        set inspection-mode proxy
        set ssl-ssh-profile "certificate-inspection"
    next
    edit 34
        set name "ALLOW_DNS"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "DNS"
        set inspection-mode proxy
    next
    edit 35
        set name "ALLOW_NTP_IPv6"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "NTP"
        set inspection-mode proxy
    next
    edit 36
        set name "ALLOW_VZW_IPv6"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "IKE"
        set inspection-mode proxy
    next
    edit 45
        set name "ALLOW_IPv6_SSH"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "SSH"
        set inspection-mode proxy
    next
    edit 48
        set name "ALLOW_ALL_IPV6"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "ALL"
    next
    set name "Allow_ping"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "ALL_ICMP6"
        set inspection-mode proxy
    next
    edit 32
        set name "ALLOW_HTTP"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "HTTP"
        set utm-status enable
        set inspection-mode proxy
        set ssl-ssh-profile "certificate-inspection"
        set av-profile "default"
        set dnsfilter-profile "default"
        set application-list "default"
    next
    edit 33
        set name "ALLOW_HTTPS"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "HTTPS"
        set inspection-mode proxy
        set ssl-ssh-profile "certificate-inspection"
    next
    edit 34
        set name "ALLOW_DNS"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "DNS"
        set inspection-mode proxy
    next
    edit 35
        set name "ALLOW_NTP_IPv6"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "NTP"
        set inspection-mode proxy
    next
    edit 36
        set name "ALLOW_VZW_IPv6"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "IKE"
        set inspection-mode proxy
    next
    edit 45
        set name "ALLOW_IPv6_SSH"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "SSH"
        set inspection-mode proxy
    next
    edit 48
        set name "ALLOW_ALL_IPV6"
        set srcintf "internal"
        set dstintf "virtual-wan-link"
        set action accept
        set srcaddr6 "all"
        set dstaddr6 "all"
        set schedule "always"
        set service "ALL"
    next



**Bonus Starlink APP**

In order to do this you need to use another interface I am selecting internal7 on it's own interface (Not part of the switch), since you can't do DHCP and static ip addresses at the same time (Lame).

    config system interface
    edit "internal7"
        set vdom "root"
        set ip 192.168.100.101 255.255.255.0
        set type physical
        set alias "Star Link MGMT"
        set device-identification enable
        set lldp-transmission enable
        set role lan
        set snmp-index 19
        config ipv6
            set ip6-allowaccess ping
        end
    next

The wiring looks like this

Starlink ---> POE ---> Switch(1)

Switch(2) ----> WAN2

Switch(3) ---> internal7

make up an untagged VLAN like 72 for simplicity port 1 is Starlink Dishy, port 2 is WAN2, port 3 is internal7

Then in procurve (That is what I am using)

    config t
        vlan 72
            untagged 1-3

then configure internal7 as above.

Create a rule to allow internal to go to the internal7 as so

    edit 60
        set name "ALLOW_TO_STARLINK_MGMT"
        set srcintf "internal"
        set dstintf "internal7"
        set action accept
        set srcaddr "Internal"
        set dstaddr "internal7 address"
        set schedule "always"
        set service "ALL"
        set nat enable
    next

That's it now you should be able to use your Starlink APP to see Dishy stats. It's important to make sure Internal7 is NOT part of your switch and it's-it's own interface.



Thanks all! 

john8675309 at the gmail dot com

John Hass



References:

[Fixed Internal Interface (Comment Section)](https://www.reddit.com/r/fortinet/comments/1542pnk/fortigate_and_ipv6_configuration_with_prefix/)

[Initial Setup Not Quite RIght](https://www.reddit.com/r/Starlink/comments/152j5b4/starlink_with_fortigate_no_ipv6/)
