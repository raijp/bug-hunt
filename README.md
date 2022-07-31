# bug-hunt
```
git clone https://github.com/raijp/bug-hunt.git && bug-hunt
```
# OWASP ZAP
```
docker build -t zap docker/owasp-zap/.

docker rm -f owasp-zap

docker run -it -d --user root --mount type=bind,source=$(pwd)/docker/owasp-zap/zap/wrk,target=/zap/wrk/,bind-propagation=shared \
--name owasp-zap zap /bin/bash

# baseline scan. check out https://www.zaproxy.org/docs/docker/baseline-scan/
docker exec -it owasp-zap zap-baseline.py -r report.html -t <url>

# full scan. https://www.zaproxy.org/docs/docker/full-scan/
docker exec -it owasp-zap zap-full-scan.py -r report.html -t <url>
```

# WPSCAN
```
docker pull mysql
docker pull wordpress
docker build -t wpscan docker/wpscan/.


docker network remove network-wpscan
docker rm -f  mysql-wpscan
docker rm -f  wordpress-wpscan
docker rm -f  wpscan


docker network create network-wpscan --subnet=172.42.0.0/16 --gateway=172.42.0.1

docker run -d --network network-wpscan -p 3316:3306 \
  -e MYSQL_ROOT_PASSWORD=password \
  --name mysql-wpscan mysql:latest
docker exec -it mysql-wpscan mysql -h 172.42.0.1 -P 3316 -u root -ppassword -e "create database wordpress;"

docker run -d --network network-wpscan -p 8901:80 \
  -e WORDPRESS_DB_HOST=172.42.0.1:3316 \
  -e WORDPRESS_DB_USER=root \
  -e WORDPRESS_DB_PASSWORD=password \
  -e WORDPRESS_DB_NAME=wordpress \
  -e WORDPRESS_TABLE_PREFIX=wp_ \
  --name wordpress-wpscan wordpress:latest

docker run -it -d --network network-wpscan --user root --entrypoint /bin/ash \
  --mount type=bind,source=$(pwd)/docker/wpscan/output,target=/output \
  --name wpscan wpscan


####
# Navigate to http://127.0.0.1:8901 on your using browser then enter username and password
####

docker exec -it wpscan /usr/local/bundle/bin/wpscan --random-user-agent -e vp -e u --url http://172.42.0.1:8901 -o /output/wpscan-output.txt
# Check options
docker exec -it wpscan /usr/local/bundle/bin/wpscan --help
```
