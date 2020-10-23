Teknologi Virtualisasi dan Container – Docker

Virtualisasi tingkat OS merupakan  sistem operasi di mana kernel mengijinkan keberadaan beberapa contoh ruang pengguna yang terisolasi.seperti container ( Docker ). 
Program komputer yang berjalan pada sistem operasi biasa dapat melihat semua sumber daya (perangkat, file dan folder yang terhubung, jaringan bersama , daya CPU, kemampuan perangkat keras yang dapat diukur) dari komputer itu. Namun, program yang berjalan di dalam container hanya dapat melihat isi container dan perangkat yang ditetapkan ke container. 
Dengan virtualisasi sistem operasi:
-	Program dapat dijalankan dalam container, yang hanya dialokasikan sebagian dari resource ini.
-	Digunakan di lingkungan hosting virtual , yang berguna untuk mengalokasikan sumber daya perangkat keras yang terbatas secara aman di antara sejumlah besar pengguna yang saling tidak percaya.
-	Tidak sefleksibel pendekatan virtualisasi lainnya karena tidak dapat meng-host sistem operasi guest yang berbeda dari host, atau kernel guest yang berbeda.

Docker
Docker adalah platform terbuka untuk mengembangkan, mengirim, dan menjalankan aplikasi, dimana aplikasi dapat dipisahkan dari infrastruktur mengirimkan perangkat lunak dengan cepat. Pengelolaan infrasruktur sama dengan pengelolaan aplikasi, mengurangi delay antara menulis kode dan menjalankannya.
Docker menyediakan kemampuan untuk mengemas dan menjalankan aplikasi di lingkungan yang terisolasi yang disebut container.  Isolasi dan keamanan memungkinkan Anda menjalankan banyak container secara bersamaan pada host tertentu. Container sangat ringan karena tidak memerlukan beban ekstra hypervisor, tetapi dijalankan langsung di dalam kernel mesin host.
User dapat menjalankan beberapa kontainer pada kombinasi perangkat keras tertentu daripada jika menggunakan mesin virtual. Docker Kontainer dapat dijalankan di dalam mesin host virtual.
Komponen utama Docker Engine :
•	Server dengan kemampuan menjalan kan program lama disebut proses daemon (perintah dockerd ).
•	REST API, menentukan antarmuka yang dapat digunakan program untuk berkomunikasi dengan daemon dan menginstruksikan apa yang harus dilakukan.
•	Command Line Interface (CLI) (perintah docker ).
         
CLI menggunakan Docker REST API untuk mengontrol atau berinteraksi dengan daemon Docker melalui skrip atau perintah CLI langsung. 

Arsitektur Docker
Daemon membuat dan mengelola objek Docker, seperti image, container, jaringan, dan volume.
Docker menggunakan arsitektur klien-server. Klien Docker berbicara dengan daemon Docker, yang melakukan pekerjaan berat membangun, menjalankan, dan mendistribusikan kontainer Docker Anda. Klien dan daemon Docker dapat berjalan di sistem yang sama, atau dapat menghubungkan klien Docker ke daemon Docker jarak jauh. Klien Docker dan daemon berkomunikasi menggunakan REST API, melalui soket UNIX atau antarmuka jaringan.
Daemon Docker ( dockerd ) mendengarkan permintaan API Docker dan mengelola objek Docker, kontainer, jaringan, dan volume. Daemon juga dapat berkomunikasi dengan daemon lain untuk mengelola layanan Docker.
Registri Docker menyimpan gambar Docker. Docker Hub adalah registri publik yang dapat digunakan siapa saja, dan Docker dikonfigurasi untuk mencari gambar di Docker Hub secara default. 
Objek Docker
Saat menggunakan Docker, Anda membuat dan menggunakan gambar, kontainer, jaringan, volume, plugin, dan objek lainnya. Bagian ini adalah gambaran singkat dari beberapa objek tersebut.

