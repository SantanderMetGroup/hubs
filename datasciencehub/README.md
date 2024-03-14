```bash
version=$(date '+%Y%m%d')
docker build -t zequihg50/datasciencehub:${version} . &>log &
docker push zequihg50/datasciencehub:${version}
```
