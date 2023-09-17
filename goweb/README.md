GO API(Docker)
==============

```bash
docker build -t komavideo/goweb:1.0 .
docker image ls
docker run --name goweb -d -p 8080:8080 komavideo/goweb:1.0
curl http://localhost:8080
```
