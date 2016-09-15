# Docker compose

> https://docs.docker.com/compose/


## Ubuntu

> https://hub.docker.com/_/ubuntu/

Build an Ubuntu container for use in the nginx docker compose.
```
docker build -f Dockerfile.ubuntu --tag myubuntu .
```

TEST

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
    command: curl -s http://nginx:80/index.html
    networks:
    - mynet

networks:
  mynet:
```
Key items to notice: images, commands, networks, ports and volumes.

Now create and run the services (containers).
```
docker-compose -f docker-compose.nginx.yml up
```
From another terminal window:
```
docker-compose -f docker-compose.nginx.yml ps
curl 0.0.0.0:[host port]
```
Find `[host port]` in the output of the `ps` command.

```
docker-compose -f docker-compose.nginx.yml down
```

Now change `"80"` to `"8080:80"` then run `docker-compose ... up`, `docker-compose ... ps` and `curl`.

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
Key items to notice: `mem_limit`, `environment` and the `seed` hostname/service.

Create and run the services (containers).
```
docker-compose -f docker-compose.cassandra.yml up
```

From another terminal shell:
```
docker-compose -f docker-compose.cassandra.yml ps
```

Run a bash shell in the seed container.
```
docker-compose -f docker-compose.cassandra.yml exec seed bash
```

From the container shell run:
```
nodetool -h localhost status
exit
```

Control-C in terminal window where you ran `docker-compose ... up`.
```
docker-compose -f docker-compose.cassandra.yml ps
```

```
docker-compose -f docker-compose.cassandra.yml down
```
