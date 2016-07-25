# Docker compose

> https://docs.docker.com/compose/


## Ubuntu

> https://hub.docker.com/_/ubuntu/

Build an Ubuntu container for use in the nginx docker compose. 
```
docker build -f Dockerfile.ubuntu --tag myubuntu .
```

## Nginx

> https://hub.docker.com/_/nginx/

File `docker-compose.nginx.yml`:
```
version: '2'
services:
  nginx:
    image: nginx
    ports:
    - "80"
    networks:
    - mynet
    volumes:
    - /Users/david/GitHub/docker-tutorial/mycompose:/usr/share/nginx/html:ro
  ubuntu:
    image: myubuntu
    command: curl http://nginx:80/index.html
    networks:
    - mynet
    links:
    - nginx

networks:
  mynet:
```

```
docker-compose -f docker-compose.nginx.yml up
```
From another terminal window:
```
docker-compose -f docker-compose.nginx.yml ps
curl 0.0.0.0:[host port]
```

```
docker-compose -f docker-compose.nginx.yml down
```

Now change `"80"` to `"8080:80"` then run `docker-compose ... up`, `ps` and `curl`.

## Cassandra cluster

> https://hub.docker.com/_/cassandra/

File `docker-compose.cassandra.yml`:
```
version: '2'
services:
  seed:
    image: cassandra
    mem_limit: 1g
    networks:
      - mynet
    environment:
      - MAX_HEAP_SIZE=768m
      - HEAP_NEWSIZE=512m
  nonseed:
    image: cassandra
    mem_limit: 2g
    networks:
    - mynet
    environment:
      - CASSANDRA_SEEDS=seed 
      - MAX_HEAP_SIZE=512m
      - HEAP_NEWSIZE=256m

networks:
  mynet:
    driver: bridge
```

```
docker-compose -f docker-compose.cassandra.yml up
```

```
docker-compose -f docker-compose.cassandra.yml ps
```

```
docker-compose -f docker-compose.cassandra.yml exec seed bash
```

```
nodetool -h localhost status
exit
```

Control-C in log window where you run `docker-compose ... up`.
```
docker-compose -f docker-compose.cassandra.yml ps
```

```
docker-compose -f docker-compose.cassandra.yml down
```