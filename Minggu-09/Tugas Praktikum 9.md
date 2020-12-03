Docker untuk Pemula – Linux

•	Tugas 0: Prasyarat
Sesi ini menyelesaikan dua pekerjaan :
-	Clone the Lab’s GitHub Repo
Gunakan perintah berikut untuk menggandakan repo lab dari GitHub. Ini akan membuat salinan repo lab di sub-direktori baru bernama linux_tweet_app.

Perintah :
git clone https://github.com/dockersamples/linux_tweet_app

hasil : 
Cloning into 'linux_tweet_app'...
remote: Enumerating objects: 14, done.
remote: Total 14 (delta 0), reused 0 (delta 0), pack-reused 14
Receiving objects: 100% (14/14), 10.76 KiB | 10.76 MiB/s, done.
Resolving deltas: 100% (5/5), done.
[node1] (local) root@192.168.0.23 ~
$ [node1] (local) root@192.168.0.23 ~

-	Mempunyai DockerID
Membuat DockerID  di https://hub.docker.com/

•	Tugas 1: Menjalankan beberapacontoh Docker Container
Menjalankan tugas dalam Alpine Linux Container.
Perintah dijalankan di Linux Console.

Jalankan perintah :
docker container run alpine hostname

Hasil :
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
188c0c94c7c5: Pull complete
Digest: sha256:c0e9560cda118f9ec63ddefb4a173a2b2a0347082d7dff7dc14272e7841a5b5a
Status: Downloaded newer image for alpine:latest
6b8f4662123e

Hostname Container ditampilkan : 6b8f4662123e

Perintah menampilkan semua container :
docker container ls --all

hasil :
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
6b8f4662123e        alpine              "hostname"          4 minutes ago       Exited (0) 4 minutes ago                       pensive_hofstadter

Jalankan container Ubuntu interaktif
Menjalankan container Docker dengan perintah :
docker container run --interactive --tty --rm ubuntu bash

Parameter diatas :
-	--interactive sesi interaktif
-	--tty  mengalokasikan pseudo-tty
-	--rm Docker untuk melanjutkan dan menghapus container setelah selesai dijalankan

Perintah lain :
ls /  menampilkan direktori
bin   dev  home  lib32  libx32  mnt  proc  run   srv  tmp  var
boot  etc  lib   lib64  media   opt  root  sbin  sys  usr

ps aux  akan menampilkan proses yang sedang berjalan 
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0   4108  3556 pts/0    Ss   11:00   0:00 bash
root        10  0.0  0.0   5896  2908 pts/0    R+   11:08   0:00 ps aux

cat / etc / issue  akan menunjukkan distro Linux mana yang menjalankan container,
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0   4108  3556 pts/0    Ss   11:00   0:00 bash
root        10  0.0  0.0   5896  2908 pts/0    R+   11:08   0:00 ps aux
root@f68bd4feca4c:/# cat /etc/issue
Ubuntu 20.04.1 LTS \n \l

Ketik exit untuk keluar dari sesi. Ini akan menghentikan proses bash, keluar dari container.

Cek versi host :
cat /etc/issue

Hasil :
	Welcome to Alpine Linux 3.12
Kernel \r on an \m (\l)


Menjalankan Background MySQL container 

1.	Menjalankan MySQL container
docker container run \
>  --detach \
>  --name mydb \
>  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
>  mysql:latest

Hasil :
Unable to find image 'mysql:latest' locally
latest: Pulling from library/mysql
852e50cd189d: Pull complete
29969ddb0ffb: Pull complete
a43f41a44c48: Pull complete
5cdd802543a3: Pull complete
b79b040de953: Pull complete
938c64119969: Pull complete
7689ec51a0d9: Pull complete
a880ba7c411f: Pull complete
984f656ec6ca: Pull complete
9f497bce458a: Pull complete
b9940f97694b: Pull complete
2f069358dc96: Pull complete
Digest: sha256:4bb2e81a40e9d0d59bd8e3dc2ba5e1f2197696f6de39a91e90798dd27299b093
Status: Downloaded newer image for mysql:latest
5213a8f202b34b600c6b9356aa87fbbac32aab2cb4251e180329cfd0a1fcb27e

2.	Melihat container aktif
Perintah :
docker container ls

hasil :
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                 NAMES
5213a8f202b3        mysql:latest        "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp, 33060/tcp   mydb


3.	menggunakan beberapa perintah Docker bawaan: docker container logs dan docker container top.
-	docker container logs mydb


hasil :
2020-12-03 11:29:06+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.22-1debian10 started.
2020-12-03 11:29:07+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2020-12-03 11:29:07+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.22-1debian10 started.
2020-12-03 11:29:07+00:00 [Note] [Entrypoint]: Initializing database files

-	docker container top mydb
hasil :
PID                 USER                TIME                COMMAND
7137                999                 0:01                mysqld

4.	Melihat versi MySQL denan perintah : docker container exec
Perintah :
mysql --user=root --password=$MYSQL_ROOT_PASSWORD –version

hasil :
mysql: [Warning] Using a password on the command line interface can be insecure.
mysql  Ver 8.0.22 for Linux on x86_64 (MySQL Community Server - GPL



•	Tugas 2: Mengemas dan menjalankan aplikasi menggunakan Docker
Build a simple website image
1.	Pastikan Anda berada di direktori linux_tweet_app.
cd ~/linux_tweet_app

2.	Tampilkan konten Dockerfile
cat Dockerfile

Hasil :
FROM nginx:latest
COPY index.html /usr/share/nginx/html
COPY linux.png /usr/share/nginx/html
EXPOSE 80 443
CMD ["nginx", "-g", "daemon off;"]

3.	Memasukkan DockerID
export DOCKERID=henarto2000



4.	echo $DOCKERID
hasil :
henarto2000

5.	Membuat image Docker baru menggunakan instruksi di Dockerfile.
Perintah :
docker image build --tag $DOCKERID/linux_tweet_app:1.0

hasil :
Sending build context to Docker daemon  92.16kB
Step 1/5 : FROM nginx:latest
latest: Pulling from library/nginx
852e50cd189d: Already exists
571d7e852307: Pull complete
addb10abd9cb: Pull complete
d20aa7ccdb77: Pull complete
8b03f1e11359: Pull complete
Digest: sha256:6b1daa9462046581ac15be20277a7c75476283f969cb3a61c8725ec38d3b01c3
Status: Downloaded newer image for nginx:latest
 ---> bc9a0695f571
Step 2/5 : COPY index.html /usr/share/nginx/html
 ---> b2df38db8957
Step 3/5 : COPY linux.png /usr/share/nginx/html
 ---> 83385dbc8ce4
Step 4/5 : EXPOSE 80 443
 ---> Running in c44c6a7c02a1
Removing intermediate container c44c6a7c02a1
 ---> 125642a79b5f
Step 5/5 : CMD ["nginx", "-g", "daemon off;"]
 ---> Running in e6972a42dfa3
Removing intermediate container e6972a42dfa3
 ---> 552487af94e3
Successfully built 552487af94e3
Successfully tagged henarto2000/linux_tweet_app:1.0

6.	Menjalankan Docker container untuk memulai container baru 

docker container run \
>  --detach \
>  --publish 80:80 \
>  --name linux_tweet_app \
>  $DOCKERID/linux_tweet_app:1.0


•	Tugas 3: Memodifikasi Situs Web yang Sedang Berjalan
Mulai aplikasi web dengan bind mount
docker container run \
>  --detach \
>  --publish 80:80 \
>  --name linux_tweet_app \
>  --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html \>  $DOCKERID/linux_tweet_app:1.0

Modifikasi website
1.	Hentikan dan hapus penampung yang sedang berjalan.
Perintah :
docker rm --force linux_tweet_app

hasil :
linux_tweet_app

2.	Jalankan kembali versi saat ini tanpa bind mount
docker container run \
>  --detach \
>  --publish 80:80 \
>  --name linux_tweet_app \
>  $DOCKERID/linux_tweet_app:1.0

Push ke Docker Hub
1.	Daftar image di Host Docker
Perintah :
docker image ls -f reference="$DOCKERID/*"
Hasil :
REPOSITORY                    TAG                 IMAGE ID   CREATED             SIZE
henarto2000/linux_tweet_app   1.0                 552487af94e3   13 minutes ago      133MB

2.	Sebelum push image, gambar Anda, Anda harus masuk ke Docker Hub
docker login

Hasil :
Username: henarto2000
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

3.	Push versi 1.0

Perintah :
	docker image push $DOCKERID/linux_tweet_app:1.0

Hasil :
The push refers to repository [docker.io/henarto2000/linux_tweet_app]
16cf907f3b88: Pushed
5d5720498986: Pushed
7e914612e366: Mounted from library/nginx
f790aed835ee: Mounted from library/nginx
850c2400ea4d: Mounted from library/nginx
7ccabd267c9f: Mounted from library/nginx
f5600c6330da: Mounted from library/nginx
1.0: digest: sha256:b36dd3f062ae3a01df48ca739889b086a101ceb143167dab785266a4186e2f20 size: 1777

Telusuri https://hub.docker.com/r/henarto2000/ dan melihat gambar Docker yang baru di push. 
Ini adalah repositori publik, jadi siapa pun dapat pull image 
Tidak memerlukan ID Docker untuk menarik gambar publik. 
Docker Hub juga mendukung repositori pribadi.

 
