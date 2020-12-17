Application Containerization and Microservice Orchestration

Langkah yang dilalui

•	Stage Setup

Dimulai dengan menggandakan repositori kode demo, mengubah direktori kerja, dan memeriksa cabang demo.

Perintah :
git clone https://github.com/ibnesayeed/linkextractor.git
cd linkextractor
git checkout demo

Hasil :
$ git clone https://github.com/ibnesayeed/linkextractor.git
Cloning into 'linkextractor'...
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 144 (delta 0), reused 0 (delta 0), pack-reused 140
Receiving objects: 100% (144/144), 44.55 KiB | 8.91 MiB/s, done.
Resolving deltas: 100% (43/43), done.
[node1] (local) root@192.168.0.13 ~
$ cd linkextractor
[node1] (local) root@192.168.0.13 ~/linkextractor
$ git checkout demo
Branch 'demo' set up to track remote branch 'demo' from 'origin'.
Switched to a new branch 'demo'

•	Step 0: Basic Link Extractor Script

Cek Branch dan List File dengan perintah :
git checkout step0
tree

hasil :
.
├── README.md
└── linkextractor.py

Melihat isi file  linkextractor.py :
cat linkextractor.py

hasil :
#!/usr/bin/env python

import sys
import requests
from bs4 import BeautifulSoup
res = requests.get(sys.argv[-1])
soup = BeautifulSoup(res.text, "html.parser")
for link in soup.find_all("a"):
    print(link.get("href"))


langkah selanjutnya ketik perintah :
./linkextractor.py http://example.com/

Hasilnya :
bash: ./linkextractor.py: Permission denied

terlihat bahwa perintah  tersebut tidak bisa diakses. Untuk mengecek hak akses, gunakan perintah :
ls -l linkextractor.py

Hasil :
-rw-r--r--    1 root     root           220 Dec 17 10:38 linkextractor.py

Hak akses -rw-r - r-- menunjukkan bahwa skrip tidak diset untuk dapat dieksekusi. Untuk mengubahnya jalankan perintah :
chmod a+x linkextractor.py

hasil dapat dilihat dengan perintah  : ls –l linkextractor.py
sehingga dihasilkan :
-rwxr-xr-x    1 root     root           220 Dec 17 10:38 linkextrac

•	Step 1: Containerized Link Extractor Script

Cek Branch dan list file di step 1:
git checkout step1
tree

hasil : 
.
├── Dockerfile
├── README.md
└── linkextractor.py

Terdapat file baru Dockerfile
Cek isi file Dockerfile :
cat Dockerfile

hasil :
FROM       python:3
LABEL      maintainer="Sawood Alam <@ibnesayeed>"

RUN        pip install beautifulsoup4
RUN        pip install requests

WORKDIR    /app
COPY       linkextractor.py /app/
RUN        chmod a+x linkextractor.py

ENTRYPOINT ["./linkextractor.py"]

Untuk mengenal Docker Image, ketik perintah :
docker image build -t linkextractor:step1

hasil :
Successfully built 8196e73c205d
Successfully tagged linkextractor:step1

Docker image telah berhasil dibuat dengan nama linkextractor:step1. Untuk melihat isinya, ketik perintah :
docker image ls

hasil :
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
linkextractor   step1     8196e73c205d   2 minutes ago   895MB
python              3         2770b69c10e1   37 hours ago    885MB

Jalankan container mengunakan imae yang baru dibuat dan ekstrak link dari beberapa halaman web aktif.
docker container run -it --rm linkextractor:step1 http://example.com/

hasil berupa link yang ada di halaman web example.com:
https://www.iana.org/domains/example


•	Step 2: Link Extractor Module with Full URI and Anchor Text

Cek branc dan list file :
git checkout step2
tree

hasil :
── Dockerfile
├── README.md
└── linkextractor.py

Skrip linkextractor.py diperbarui dengan perubahan fungsi :
-	Jalur dinormalisasi menjadi URL lengkap
-	Melaporkan tautan dan teks tautan
-	Dapat digunakan sebagai modul di skrip lain
Perintah berikut untuk mengecek perubahannya :
cat linkextractor.py

hasil :
#!/usr/bin/env python
import sys
import requests
from bs4 import BeautifulSoup
res = requests.get(sys.argv[-1])
soup = BeautifulSoup(res.text, "html.parser")
for link in soup.find_all("a"):
    print(link.get("href"))

Buat imae baru pada step 2 :
docker image build -t linkextractor:step2 .

hasil :
 Successfully built 8196e73c205d
Successfully tagged linkextractor:step2

Image di step 2 ini tidak akan menganti image yang dibuat di step 0.
docker image ls

hasil  :
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
linkextractor   step1     8196e73c205d   16 minutes ago   895MB
linkextractor   step2     8196e73c205d   16 minutes ago   895MB
python          3         2770b69c10e1   37 hours ago     885MB

Menjalankan container dengan image di step 2 :
docker container run -it --rm linkextractor:step2 https://training.play-with-docker.com/

Hasil nya akan sedikit berbeda  :
g.play-with-docker.com/
/
/about/
#ops
#dev
/ops-stage1
/ops-stage2
/ops-stage3
/dev-stage1
/dev-stage2
/dev-stage3
/alacart
https://twitter.com/intent/tweet?text=Play with Docker Classroom&url=https://training.play-with-docker.com/&via=docker&related=docker
https://facebook.com/sharer.php?u=https://training.play-with-docker.com/
https://plus.google.com/share?url=https://training.play-with-docker.com/
http://www.linkedin.com/shareArticle?mini=true&url=https://training.play-with-docker.com/&title=Play%20with%20Docker%20Classroom&source=https://training.play-with-docker.com
https://www.docker.com/dockercon/
https://www.docker.com/dockercon/
https://dockr.ly/slack
https://www.docker.com/legal/docker-terms-service
https://www.docker.com
https://www.facebook.com/docker.run
https://twitter.com/docker
https://www.github.com/play-with-docker/play-with-docker.github.io


•	Step 3: Link Extractor API Service

Periksa image di step 3
git checkout step3
tree

hasil :

├── Dockerfile
├── README.md
└── linkextractor.py

Cek perubahan pada file docker :
cat Dockerfile

hasil :
FROM       python:3
LABEL      maintainer="Sawood Alam <@ibnesayeed>"

RUN        pip install beautifulsoup4
RUN        pip install requests

WORKDIR    /app
COPY       linkextractor.py /app/
RUN        chmod a+x linkextractor.py

ENTRYPOINT ["./linkextractor.py"]

Tidak ada perubahan pada modul linkextractor.py. Cek file baru  main.py :


Membuat image baru :
docker image build -t linkextractor:step3

hasil :
Successfully built 94c662c39c3c
Successfully tagged linkextractor:step3

Jalankan container docker :
docker container run -d -p 5000:5000 --name=linkextractor linkextractor:step3

hasil :
d6b40e0f8fe44b188b8a6e0528134eb92dad28669cbb6e053a3811ab9ecfa0b9


•	Step 4: Link Extractor API and Web Front End Services

Cek branch dan file masih seperti step sebelumnya :
├── Dockerfile
├── README.md
└── linkextractor.py

•	Step 5: Redis Service for Caching

Cek branch dan file masih seperti step sebelumnya :
├── Dockerfile
├── README.md
└── linkextractor.py



•	Step 6: Swap Python API Service with Ruby

Cek Branch dan file :
git checkout step6
tree

hasil : 
.
├── README.md
├── api
│   ├── Dockerfile
│   ├── Gemfile
│   └── linkextractor.rb
├── docker-compose.yml
├── logs
└── www
    ├── Dockerfile
    └── index.php

Cek file yang berubah :
cat api/linkextractor.rb

hasil :
#!/usr/bin/env ruby
# encoding: utf-8

require "sinatra"
require "open-uri"
require "uri"
require "nokogiri"
require "json"
require "redis"

set :protection, :except=>:path_traversal

redis = Redis.new(url: ENV["REDIS_URL"] || "redis://localhost:6379")

Dir.mkdir("logs") unless Dir.exist?("logs")
cache_log = File.new("logs/extraction.log", "a")

get "/" do
  "Usage: http://<hostname>[:<prt>]/api/<url>"
end

get "/api/*" do
  url = [params['splat'].first, request.query_string].reject(&:empty?).join("?")
  cache_status = "HIT"
  jsonlinks = redis.get(url)
  if jsonlinks.nil?
    cache_status = "MISS"
    jsonlinks = JSON.pretty_generate(extract_links(url))
    redis.set(url, jsonlinks)
  end
  cache_log.puts "#{Time.now.to_i}\t#{cache_status}\t#{url}"

  status 200
  headers "content-type" => "application/json"
  body jsonlinks
end

def extract_links(url)
  links = []
  doc = Nokogiri::HTML(open(url))
  doc.css("a").each do |link|
    text = link.text.strip.split.join(" ")
    begin
      links.push({
        text: text.empty? ? "[IMG]" : text,
        href: URI.join(url, link["href"])
      })
    rescue
    end
  end
  links
end

file lain :
cat api/Dockerfile

hasil :
FROM       ruby:2.6
LABEL      maintainer="Sawood Alam <@ibnesayeed>"

ENV        LANG C.UTF-8
ENV        REDIS_URL="redis://localhost:6379"

WORKDIR    /app
COPY       Gemfile /app/
RUN        bundle install

COPY       linkextractor.rb /app/
RUN        chmod a+x linkextractor.rb

CMD        ["./linkextractor.rb", "-o", "0.0.0.0"]

Cek juga file :
cat docker-compose.yml

hasil :
version: '3'

services:
  api:
    image: linkextractor-api:step6-ruby
    build: ./api
    ports:
      - "4567:4567"
    environment:
      - REDIS_URL=redis://redis:6379
    volumes:
      - ./logs:/app/logs
  web:
    image: linkextractor-web:step6-php
    build: ./www
    ports:
      - "80:80"
    environment:
      - API_ENDPOINT=http://api:4567/api/
  redis:
    image: redis

service stack :
docker-compose up -d --build

hasil :
Status: Downloaded newer image for redis:latest
Creating linkextractor_web_1   ... done
Creating linkextractor_api_1   ... done
Creating linkextractor_redis_1 ... done

Akses API :
curl -i http://localhost:4567/api/http://example.com/

Hasil :
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 97
X-Content-Type-Options: nosniff
Server: WEBrick/1.4.2 (Ruby/2.6.6/2020-03-31)
Date: Thu, 17 Dec 2020 11:59:28 GMT
Connection: Keep-Alive

[
  {
    "text": "More information...",
    "href": "https://www.iana.org/domains/example"
  }

Menutup stack :
docker-compose down

hasil :
Stopping linkextractor_redis_1 ... done
Stopping linkextractor_web_1   ... done
Stopping linkextractor_api_1   ... done
Removing linkextractor_redis_1 ... done
Removing linkextractor_web_1   ... done
Removing linkextractor_api_1   ... done
Removing network linkextractor_default

Log masih ada walaupun service sudah di matikan :
cat logs/extraction.log

Hasil :
1608206368      MISS    http://example.com/



