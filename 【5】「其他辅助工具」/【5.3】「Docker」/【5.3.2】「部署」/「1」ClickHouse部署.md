

```
docker run -d --name clickhouse-server --ulimit nofile=262144:262144 -p 9000:9000 yandex/clickhouse-server:1.1

mkdir etc
mkdir data

docker run -it --rm --entrypoint=/bin/bash -v $PWD:/work --privileged=true --user=root yandex/clickhouse-server:1.1
cp -r /etc/clickhouse-server/* /work/etc/
exit


docker run -d --name clickhouse-server \
	--ulimit nofile=262144:262144 \
	-p 9000:9000 \
	-v $PWD:/etc/clickhouse-server \
	-v $PWD/data:/var/lib/clickhosue \
	--privileged=true --user=root \
	yandex/clickhouse-server:1.1
```



```
## 运行
docker exec -it clickhosue-server /bin/bash
clickhouse-client
```

