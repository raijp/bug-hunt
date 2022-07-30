# bug-hunt
```
git clone https://github.com/raijp/bug-hunt.git && bug-hunt

docker rm -f owasp-zap

docker run -it -d --user root --mount type=bind,source=$(pwd)/docker/owasp-zap/zap/wrk,target=/zap/wrk/,bind-propagation=shared \
--name owasp-zap owasp/zap2docker-stable /bin/bash

docker exec -it owasp-zap zap-baseline.py -r report.html -t http://172.41.0.1:8082
```
