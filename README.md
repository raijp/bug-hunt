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
docker build -t wpscan docker/wpscan/.

docker rm -f  wpscan

docker run -it -d --entrypoint /bin/ash --name wpscan1 wpscan
docker exec -it wpscan1 /usr/local/bundle/bin/wpscan --random-user-agent --url <url>
# Check options
docker exec -it wpscan1 /usr/local/bundle/bin/wpscan --help
```
