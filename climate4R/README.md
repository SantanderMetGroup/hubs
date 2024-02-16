Docker image for meteohub

```bash
version=$(date '+%Y%m%d')
docker build --no-cache -t santandermetgroup/meteohub:${version} .
docker push santandermetgroup/meteohub:${version}
```
