Docker Networking

Tasks:
•	Sesi #1 - Networking Basics
Langkah 1: Perintah Docker Network
Perintah Docker Network adalah perintah utama untuk mengkonfigurasi dan mengelola jaringan Container. Jalankan perintah berikut terminal pertama.

docker network

Hasil :
Usage:  docker network COMMAND
Manage networks
Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks
  
Langkah 2: Buat daftar jaringan
Perintah : 

docker network ls

Hasil :

Perintah tersebut menampilkan jaringan container yang dibuat sebagai bagian dari penginstalan standar Docker. Jaringan yang baru buat juga akan ditampilkan di output dari perintah ls.
Setiap jaringan punya ID dan NAMA yang unik. Setiap jaringan juga mempuyai satu driver. Perhatikan bahwa jaringan "bridge" dan jaringan "host" memiliki nama yang sama dengan drivernya masing-masing.
Langkah 3: Periksa jaringan

docker network inspect 

hasil :
[
    {
        "Name": "bridge",
        "Id": "1e783b5753880e8384cba7595a3d782e1d23216ccf74777d1121d76fab2a57e0",
        "Created": "2020-12-08T12:02:29.45643397Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
Langkah 4: Buat daftar plugin network driver 
docker info

Hasil :
Client:
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 19.03.11
 Storage Driver: overlay2
  Backing Filesystem: xfs
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc version: dc9208a3303feef5b3839f4323d9beb36df0a9dd
 init version: fec3683
 Security Options:
  apparmor
  seccomp
   Profile: default
 Kernel Version: 4.4.0-197-generic
 Operating System: Alpine Linux v3.12 (containerized)
 OSType: linux
 Architecture: x86_64
 CPUs: 8
 Total Memory: 31.42GiB
 Name: node1
 ID: CL43:HCZ2:2XTW:XM7Z:2GYC:6WMA:2RHS:7QU4:GCC4:MHSM:HFQH:DZSJ
 Docker Root Dir: /var/lib/docker
 Debug Mode: true
  File Descriptors: 24
  Goroutines: 43
  System Time: 2020-12-08T12:20:42.061567523Z
  EventsListeners: 0
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: true
 Insecure Registries:
  127.0.0.1
  127.0.0.0/8
 Live Restore Enabled: false
 Product License: Community Engine

WARNING: API is accessible on http://0.0.0.0:2375 without encryption.
         Access to the remote API is equivalent to root access on the host. Refer
         to the 'Docker daemon attack surface' section in the documentation for
         more information: https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface
WARNING: No swap limit support
WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disabled

•	Sesi #2 - Bridge Networking
Langkah 1: Dasar
Verifikasi instalasi Docker dengan perintah : 
docker network ls

Hasil :

 
Jaringan dan driver selalu terhubung, tetapi keduanya tidak sama. Dalam contoh ini, jaringan dan driver memiliki nama yang sama - tetapi keduanya bukan hal yang sama!
Output di atas menunjukkan bahwa jaringan bridge tercakup secara lokal, jaringan hanya ada di host Docker.
Instal perintah brctl dan gunakan untuk mendaftar jembatan Linux di host Docker menjalankan sudo apt-get install bridge-utils
apk update
apk add bridge

hasil :
 
 
Kemudian, daftarkan jembatan di host Docker Anda, dengan menjalankan brctl show.
brctl show

Hasil :
 
Output di atas menunjukkan Linux Bridge tunggal yang disebut docker0, dibuat secara otomatis untuk jaringan bridge.

Anda juga dapat menggunakan perintah ip a untuk melihat detail jembatan docker0.
ip 

Hasil : 
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue stateDOWN
    link/ether 02:42:e6:99:3d:37 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
4185: eth0@if4186: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
    link/ether 02:0a:03:0a:b0:48 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.23/23 scope global eth0
       valid_lft forever preferred_lft forever
4193: eth1@if4194: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
    link/ether 02:42:ac:12:00:57 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.87/16 scope global eth1
       valid_lft forever preferred_lft forever

Langkah 2: Hubungkan container
Jaringan Bridge adalah jaringan default untuk kontainer baru. Kecuali Anda menentukan jaringan yang berbeda, semua kontainer baru akan terhubung ke jaringan bridge.
Membuat container baru :
docker run -dt ubuntu sleep infinity
Hasil :
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
da7391352a9b: Pull complete
14428a6d4bcd: Pull complete
2c2d948710f2: Pull complete
Digest: sha256:c95a8e48bf88e9849f3e0f723d9f49fa12c5a00cfc6e60d2bc99d87555295e4c
Status: Downloaded newer image for ubuntu:latest
9fb181e86dcd06672d5d255924858ff3ab95c8ff123fe8185231581168b1ac25
Perintah ini akan membuat kontainer baru berdasarkan ubuntu: image. Untuk  memverifikasi bahwa wadah contoh kami sudah aktif dengan menjalankan docker ps.
docker ps
hasil :
 
Jalankan perintah brctl show kembali :
 
untuk memeriksa jaringan bridge lagi, jalankan perintah :
docker network inspect bridge

hasil :
[
    {
        "Name": "bridge",
        "Id": "1e783b5753880e8384cba7595a3d782e1d23216ccf74777d1121d76fab2a57e0",
        "Created": "2020-12-08T12:02:29.45643397Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "9fb181e86dcd06672d5d255924858ff3ab95c8ff123fe8185231581168b1ac25": {
                "Name": "thirsty_goldwasser",
                "EndpointID": "cc26a03b2d6bb73652038679035487aafe46ff06a3ba88ecd4583f694e9c9d71",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
Langkah 3: Uji konektivitas jaringan
Pada contoh sebelumnya adalah "172.17.0.2”..
Ping alamat IP container dengan menjalankan ping -c5 <IPv4 Address>. 

Hasil :
 PING 172.17.0.2 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.817 ms
64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.088 ms
64 bytes from 172.17.0.2: seq=2 ttl=64 time=0.112 ms
64 bytes from 172.17.0.2: seq=3 ttl=64 time=0.127 ms
64 bytes from 172.17.0.2: seq=4 ttl=64 time=0.084 ms

--- 172.17.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 0.084/0.245/0.817 ms

Kita perlu ID container pada langkah sebelumnya. Jalankan perintah : 
docker ps
Hasil :
 
Selanjutnya, jalankan shell di dalam container ubuntu, 
docker exec -it <CONTAINER ID> / bin / bash

Hasil :
 
Selanjutnya, instal ping program  dengan perintah :
apt-get update && apt-get install -y iputils-ping
hasil :
root@9fb181e86dcd:/# apt-get update && apt-get install -y iputils-ping
Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [109 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal InRelease [265 kB]
Get:3 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [103 kB]
Get:4 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [1165 B]
Get:5 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [643 kB]
Get:6 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [489 kB]
Get:7 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:8 http://archive.ubuntu.com/ubuntu focal-backports InRelease [101 kB]
Get:9 http://archive.ubuntu.com/ubuntu focal/main amd64 Packages [1275 kB]
Get:10 http://archive.ubuntu.com/ubuntu focal/universe amd64 Packages [11.3 MB]
Get:11 http://archive.ubuntu.com/ubuntu focal/restricted amd64 Packages [33.4 kB]
Get:12 http://archive.ubuntu.com/ubuntu focal/multiverse amd64 Packages [177kB]
Get:13 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [875 kB]
Get:14 http://archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 Packages [30.4 kB]
Get:15 http://archive.ubuntu.com/ubuntu focal-updates/restricted amd64 Packages [136 kB]
Get:16 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [878 kB]
Get:17 http://archive.ubuntu.com/ubuntu focal-backports/universe amd64 Packages [4248 B]
Fetched 16.6 MB in 3s (5477 kB/s)
Reading package lists... Done
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libcap2 libcap2-bin libpam-cap
The following NEW packages will be installed:
  iputils-ping libcap2 libcap2-bin libpam-cap
0 upgraded, 4 newly installed, 0 to remove and 0 not upgraded.
Need to get 90.5 kB of archives.
After this operation, 333 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu focal/main amd64 libcap2 amd64 1:2.32-1 [15.9 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal/main amd64 libcap2-bin amd64 1:2.32-1 [26.2 kB]
Get:3 http://archive.ubuntu.com/ubuntu focal/main amd64 iputils-ping amd64 3:20190709-3 [40.1 kB]
Get:4 http://archive.ubuntu.com/ubuntu focal/main amd64 libpam-cap amd64 1:2.32-1 [8352 B]
Fetched 90.5 kB in 0s (212 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libcap2:amd64.
(Reading database ... 4121 files and directories currently installed.)
Preparing to unpack .../libcap2_1%3a2.32-1_amd64.deb ...
Unpacking libcap2:amd64 (1:2.32-1) ...
Selecting previously unselected package libcap2-bin.
Preparing to unpack .../libcap2-bin_1%3a2.32-1_amd64.deb ...
Unpacking libcap2-bin (1:2.32-1) ...
Selecting previously unselected package iputils-ping.
Preparing to unpack .../iputils-ping_3%3a20190709-3_amd64.deb ...
Unpacking iputils-ping (3:20190709-3) ...
Selecting previously unselected package libpam-cap:amd64.
Preparing to unpack .../libpam-cap_1%3a2.32-1_amd64.deb ...
Unpacking libpam-cap:amd64 (1:2.32-1) ...
Setting up libcap2:amd64 (1:2.32-1) ...
Setting up libcap2-bin (1:2.32-1) ...
Setting up libpam-cap:amd64 (1:2.32-1) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 76.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install theTerm::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.30.0 /usr/local/share/perl/5.30.0 /usr/lib/x86_64-linux-gnu/perl5/5.30 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.30 /usr/share/perl/5.30 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up iputils-ping (3:20190709-3) ...
Processing triggers for libc-bin (2.31-0ubuntu9.1) ...
Kemudian ping  www.github.com :
ping -c5 www.github.com
hasil : 
root@9fb181e86dcd:/#   ping -c5 www.github.com
PING github.com (140.82.113.4) 56(84) bytes of data.
64 bytes from lb-140-82-113-4-iad.github.com (140.82.113.4): icmp_seq=1 ttl=51 time=0.990 ms
64 bytes from lb-140-82-113-4-iad.github.com (140.82.113.4): icmp_seq=2 ttl=51 time=1.12 ms
64 bytes from lb-140-82-113-4-iad.github.com (140.82.113.4): icmp_seq=3 ttl=51 time=1.06 ms
64 bytes from lb-140-82-113-4-iad.github.com (140.82.113.4): icmp_seq=4 ttl=51 time=1.11 ms
64 bytes from lb-140-82-113-4-iad.github.com (140.82.113.4): icmp_seq=5 ttl=51 time=1.03 ms

--- github.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4007ms
rtt min/avg/max/mdev = 0.990/1.060/1.121/0.047 ms

Perintah exit untuk keluar dari shell :
exit

hasil :
 
Kita juga perlu berhenti dari container ini jadi kita membersihkan semuanya dari pengujian, dengan menjalankan docker stop <CONTAINER ID>.
 

•	Sesi #3 - Overlay Networking
Langkah 1: Dasar
Langkah ini menginisialisasi Swarm baru, bergabung dengan single worker node, dan memverifikasi operasi tersebut berhasil.
docker swarm init --advertise-addr $(hostname -i

Langkah 2: Buat jaringan overlay
docker network create -d overlay overnet

Langkah 3: Buat layanan
docker service create --name myservice \
--network overnet \
--replicas 2 \
ubuntu sleep infinity

Langkah 4: Uji jaringan
docker network inspect overnet

Langkah 5: Uji penemuan layanan

•	Cleaning Up
Jalankan perintah docker service rm myservice untuk menghapus layanan yang disebut myservice.
docker service rm myservice




