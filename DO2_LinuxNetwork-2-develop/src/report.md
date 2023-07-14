## Part 1. `ipcalc` tool
- > NOTE <br> `ipcalc` is a utility that can perform simple manipulations of IPv4 addresses.

#### 1.1. Networks and Masks


1. Network address of `192.167.38.54/13:`

    ![nw-address](./ss/nw-address.png)

    - > NOTE <br> `192.160.0.0/13`

1. Conversion of the mask: 
    1. `255.255.255.0` (prefix and binary):

        ![conversion-0](./ss/conversion-0.png)

        - > NOTE <br>
            > - `/24` prefix
            > - `11111111.11111111.11111111.00000000` binary


    1. `/15` (normal and binary):

        ![conversion-1](./ss/conversion-1.png)

        - > NOTE <br>
            > - `255.254.0.0` normal
            > - `11111111.11111111.00000000.00000000` binary

    1. `11111111.11111111.11111111.11110000` (normal and prefix):

        ![conversion-2](./ss/conversion-2.png)

        - > NOTE <br>
            > - `255.255.255.240` normal
            > - `28` prefix


1. Minimum and maximum host in `12.167.38.4` network with masks:
    1. `/8`

        ![mm-host-0](./ss/mm-host-0.png)

        - > NOTE <br>
            > - `12.0.0.1 ` min
            > - `12.255.255.254` max

    1. `11111111.11111111.00000000.00000000`

        ![mm-host-1](./ss/mm-host-1.png)

        - > NOTE <br>
            > - `12.167.0.1 ` min
            > - `12.167.255.254` max

    1. `255.255.254.0`

        ![mm-host-2](./ss/mm-host-2.png)

        - > NOTE <br>
            > - `12.167.38.1` min
            > - `12.167.39.254` max

    1. `/4`

        ![mm-host-3](./ss/mm-host-3.png)

        - > NOTE <br>
            > - `0.0.0.1 ` min
            > - `15.255.255.254` max


#### 1.2. localhost

- > NOTE <br> There are classifications of IP addresses as "private" and "public". The following ranges of addresses are reserved for private (aka LAN) networks:
    > - *10.0.0.0* — *10.255.255.255* (*10.0.0.0/8*),
    > - *172.16.0.0* — *172.31.255.255* (*172.16.0.0/12*),
    > - *192.168.0.0* — *192.168.255.255* (*192.168.0.0/16*).
    > - *127.0.0.0* — *127.255.255.255* (Reserved for loopback interfaces (not used for communication between network nodes), so called localhost)

- Thus, only these IPs can access the app: 
    - `127.0.0.2`,
    - `127.1.0.1`

- > NOTE <br> Distinct `Loopback` classification (and lack thereof) proves it:

    ![loopback-proof](./ss/loopback-proof.png)

#### 1.3. Network ranges and segments

1. This listed IPs can be used as public:  
    - `134.43.0.2`,
    - `172.0.2.1`,
    - `192.172.0.1`,
    - `172.68.0.2`,
    - `192.169.168.1`

1. This listed IPs can be used as private:  
    - `10.0.0.45`,
    - `192.168.4.2`,
    - `172.20.250.4`,
    - `172.16.255.255`,
    - `10.10.10.10`

    - > NOTE <br> Distinct `Private Internet` classification (and lack thereof) proves the listing:

    ![ranges-proof](./ss/ranges-proof.png)

1. Following gateway IP addresses are possible for `10.10.0.0/18` network:
    - `10.10.0.2`,
    - `10.10.10.10`,
    - `10.10.1.255`


## Part 2. Static routing between two machines


- > NOTE <br> 
    > 1. I cloned `w1` Virtual Machine and named it `w2.`
    > 1. Then, I created Internal Network in `VirtualBox` GUI named `vboxnet0` without DHCP-server.
    > 1. For both `w1` and `w2` VMs I changed their interfaces types from default `NAT` to `Internal Network`, named `vboxnet0` 



1. `ip a` output:

    ![existing-nifs](./ss/existing-nifs.png)

    - > NOTE <br> since there isn't any DHCP-servers present, both machines don't have IPs

1. Configuring `netplan:`

    ![netplan-config](./ss/netplan-config.png)

    - > NOTE <br> `enp0s3` interfaces on both machines are responsible for `internal network.` 


1. Applying config: 

    ![netplan-config-apply](./ss/netplan-config-apply.png)

    - > NOTE <br> Now both machines have static IPs


#### 2.1. Adding a static route manually

    
![static-route-ping](./ss/static-route-ping.png)


#### 2.2. Adding a static route with saving


1. Rebooting VMs

1. Adding and checking permanent static route:

    ![permanent-sr](./ss/perma-sr.png)


## Part 3. `iperf3` utility


#### 3.1. Connection speed


- > NOTE <br> The basic unit of information transfer rate is bits per second (bps). <br> The difference between bytes per second (B/s) and bps is the same as the difference between bytes and bits: 1 B/s = 8 bps. <br> Similarly, the difference between kilobytes per second (KB/s) and B/s is the same as the difference between kilobytes and bytes: 1 KB/s = 1024 B/s. And so on.

- Thus:
    1. `8 Mbps` == `1 MB/s`
    1. `100 MB/s` == `800000 Kbps`
    1. `1 Gbps` == `1024 Mbps`


#### 3.2. **iperf3** utility


- > NOTE 
    > - `iperf3` is a program for performing real-time network throughput measurements. It is one of the powerful tools for testing the maximum achievable bandwidth in IP networks (supports IPv4 and IPv6).
    > - I also had to add another interface with DHCP-server on, just to have access to repositories.


1. To test network throughput, we connect to the remote machine which we will use as the server (`w1` in my case) and use: `iperf3 --server`
    
    ![iperf-server](./ss/iperf-server.png)

1. Then on our local machine which we will treat as the client (`w2` in my case), we use `iperf3` in client mode using:  `iperf3 --client 192.168.100.10`

    ![iperf-speed](./ss/iperf-speed.png)


## Part 4. Network firewall


#### 4.1. `iptables` utility

- > NOTE <br>  **iptables** is a command-line firewall utility that uses policy chains to allow or block traffic. When a connection tries to establish itself on your system, **iptables** looks for a rule in its list to match it to. If it doesn’t find one, it resorts to the default action.


    ![iptabels](./ss/iptables.png)

    - > NOTE 
       > - `w1` firewall strategy **denies** every incoming pings
       > - `w2` firewall strategy **accepts** every incoming pings


#### 4.2. **nmap** utility

- > NOTE <br>  `Nmap` is Linux command-line tool for network exploration and security auditing. This tool is generally used by hackers and cybersecurity enthusiasts and even by network and system administrators. It is used for the following purposes:
    > - Real time information of a network
    > - Detailed information of all the IPs activated on your network
    > - Number of ports open in a network
    > - Provide the list of live hosts
    > - Port, OS and Host scanning


![nmap](./ss/nmap.png)


## Part 5. Static network routing


![part5_network](../misc/images/part5_network.png)


- Create 5 machines:

    ![5machines](./ss/task5_1_yallVM.png)

- Configuration:

    1. `w11:`

        ![w11-config](./ss/task5_2_ws11.png)

    1. `r1:`

        ![r1-config](./ss/task5_3_r1.png)

    1. `r2:`

        ![r2-config](./ss/task5_4_r2.png)

        `ping:`

        ![ping-r2](./ss/task5_5_r2.png)

    1. `w21:`

        ![w21-config](./ss/task5_6_ws21.png)

        `ping:`
        ![ping-w21](./ss/task5_7_ws21.png)

    1. `w22:`

        ![w22-config](./ss/task5_8_ws22.png)

        `ping:`
        
        ![ping-w22](./ss/task5_9_ws22.png)


#### 5.2. Enabling IP forwarding.


1. `r1:`

    - Temporary: 

        ![r1-tmp-forward](./ss/task5-2_10_r1.png)

    - Permanent:

        ![r1-perm-forward](./ss/task5-2_11_r1.png)

1. `r2:`

    - Temporary: 

        ![r2-tmp-forward](./ss/task5-2_12_r2.png)

    - Permanent:

        ![r2-perm-forward](./ss/task5-2_13_r2.png)


#### 5.3. Default route configuration



1. Default GW configuration for:

    1. `w22:`

        ![w22-gw](./ss/task5-3_14_ws22.png)

    1. `w21:`

        ![w21-gw](./ss/task5-3_15_ws21.png)

    1. `w11:`

        ![w11-gw](./ss/task5-3_16_ws11.png)

1. Reaching `r2` from `w11:`

    ![tcpdump](./ss/task5-3_17_r2.png)

    ![tcpdumpTo](./ss/task5-3_18_ws11.png)


#### 5.4. Adding static routes



1. `r1:`

    ![r1-routes](./ss/task5-4_19_r1.png)

1. `r2:`

    ![r2-routes](./ss/task5-4_20_r2.png)


1. Showing `w11` routes:

    ![ws11-routes](./ss/task5-4_21_ws11.png)

    - > NOTE <br> `default` route has much lower priority than manually inserted one's, that is the reason why `0.0.0.0/0` and `10.10.0.0/18` differ.


#### 5.5. Making a router list


- `w11` to `w21` route path:

    ![w11-rpath](./ss/task5-5_22_r1.png)

    ![route](./ss/task5-5_23_ws11.png)


- > NOTE <br> `traceroute` attempts to trace trace the route an IP packet would follow to some internet host by launching probe packets with a small `ttl` (time to live) then listening for an `ICMP` "time exceeded" reply from a gateway. We start our probes with a `ttl` of one and increase by one untill we get an `ICMP` "port unreachable" (or TCP reset), which means we got to the "host", or hit a max (which defaults to 30 `hops`). Three probes (by default) are sent each `ttl` setting and a line is printed showing the `ttl`, address of the gateway ad round trip time of each probe. The address can be followed by additional information when requested. If the probe answers come from different gateways, the address of each responding system will be printed. If there is no response within a certain timeout, an "\*" (asterisk) is printed for that probe.


#### 5.6. Using **ICMP** protocol in routing


- > NOTE <br> The **ICMP** protocol is an error notification protocol. The TCP/IP stack has a special messaging mechanism to allow routers to notify network nodes of errors or abnormal situations, called the Internet Control Message Protocol (**ICMP**).


- Capturing ping reply from non-existent IP:

    ![ping-icmp](./ss/task5-6_24_r1.png)

    ![ping-icmp](./ss/task5-6_25_ws11.png)


## Part 6. Dynamic IP configuration using `DHCP`


- > NOTE <br> **DHCP** is a TCP/IP model application layer protocol used to assign an IP address to a client. An IP address can be manually assigned to each client, i.e. to a computer within a local network. But in large networks it’s very time-consuming and moreover, the larger the local network, the higher the chance of configuration error. This is why the **DHCP** protocol was created to automate IP assignment.

1. Configuring `r2` DHCP service:

    1. */etc/dhcp/dhcpd.conf:* 
        
        ![r2-dhcpd](./ss/r2-dhcpd.png)

    1. */etc/resolv.conf:*

        ![r2-resolv](./ss/r2-resolv.png)

    1. Restarting DHCP service: `systemctl restart isc-dhcp-server`

        ![r2-dhcp-restart](./ss/r2_by6task.png)

    1. Confirming that service is working:

        ![r2-dhcp-confirm](./ss/r2-dhcp-confirm.png)

1. Configuring `r1` DHCP service:

    1. Configuring `r1` strictly on the MAC-addresses:

        ![r1-dhcpd](./ss/r1-dhcpd.png)

    1. */etc/resolv.conf:*

        ![r1-resolv](./ss/r1-resolv.png)

    1. Restarting DHCP service: `systemctl restart isc-dhcp-server`

        ![r1-dhcp-restart](./ss/r1by6task.png)

    1. Confirming that service is working:

        ![r1-dhcp-confirm](./ss/r1-dhcp-confirm.png)

1. Requesting IP address from `w21`

    ![w21-requesting-ip](./ss/w21-requesting-ip.png)

    - > NOTE <br> Since IP was assigned immediately after DHCP server restart, we had to release it, using `dhclient -r` (`-r` stands for `release`), which left us without any IP whatsoever. After that, we used `dhclient` thus requesting IP renewal.  


## Part 7. ``NAT``


1. Turning `Apache2` server into a public one:

    ![apache-pub](./ss/apache-pub.png)

1. Starting `apache2` service:

    ![apache-start](./ss/apache-start.png)


1. Adding a *firewall:*

    ![firewall](./ss/firewall.png)

    - Poking with `ping` to check that `firewall` is working properly:

        ![firewall-blocking](./ss/firewall-blocking.png)

1. Allowing ICMP packets:

    ![icmp-passage](./ss/icmp-passage.png)

1. Enabling `SNAT` and `DNAT:`

    ![s-d-nat](./ss/s-d-nat.png)

1. Checking TCP connection for `SNAT`, using `telnet:`

    - > NOTE <br> `telnet` (short for "teletype network") is a client/server application protocol that provides access to virtual terminals of remote systems on local area networks or the Internet. *Bad ssh.*

    ![w22-to-r1](./ss/w22-to-r1.png)

    ![r1-to-w22](./ss/r1-to-w22.png)


## Part 8. Bonus. Introduction to ``SSH Tunnels``

- > NOTE <br> `ssh tunnel` or `ssh port forwarding` is a a method of transporting arbitrary data over SSH connection. SSH tunnels allow connections made to a local port (that is, to a port on your own desktop) to be forwarded to a remote machine via a secure channel. That's what we gonna do now! I want to die! Yay!


1. Running a firewall:

- > NOTE <br> Not really a command, we made this firewall along this project.

    ![running-firewall](./ss/running-firewall.png)

1. Starting `Apache:`

- > NOTE <br> we used `apache2` specifically, its some kind of a free web server something something, I don't really know, I'm rushed... 

    ![apache-start](./ss/apache-start.png)

1. Accessing `w22` from `w21` locally:

    - > NOTE <br> `ssh` is a yet another cool protocol, but this time its for `Shell`, meaning that it is designed to operate on the command line. Most notable its use for remote login and command-line execution!

    ![accessing-l](./ss/Ltask8.png)

1. Accessing `w22` from `w11` remotely:

    ![accessing-r](./ss/Rtask8.png)

    - Checking through `telnet` connection to `localhost:`

        - `w11:`

            ![w11-access-check](./ss/w11-access-check.png)
        
        - `w21:`

            ![w21-access-check](./ss/w21-access-check.png)

