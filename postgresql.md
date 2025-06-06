---
title: Linux 部署 postgreSQL 辅助文档
tags: Linux, PostgreSQL, Docker
---

## 部署postgreSQL 数据库
> postgreSQL 数据库是一款开源的关系型数据库，可以满足大量的应用场景。下文将介绍如何在 Linux centos上使用docker部署 postgresql 数据库。

### 准备工作

- 创建数据目录

```bash
mkdir -p /root/dockers-data/postgreSQL/data
```

- 切换到postgreSQL目录

```bash
cd /root/dockers-data/postgreSQL
```

- 创建 docker-compose.yml 文件

```bash
vim docker-compose.yml
```

- 复制 docker-compose.yml 文件

```yaml
services:
  postgres:
    image: postgres:15
    container_name: postgres
    restart: unless-stopped 
    environment:
      POSTGRES_USER: postgreRoot       
      POSTGRES_PASSWORD: FHai&000
      PGDATA: /var/lib/postgresql/data/pgdata
    volumes:
      - ./data:/var/lib/postgresql/data/pgdata
    ports:
      - "5432:5432"
    networks:
      - docker-network
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"

networks:
  docker-network:
    external: true
```
### 创建docker-network网络
```bash
docker network create docker-network
```

### 启动数据库

```bash
docker compose up -d
```

### 连接数据库

```bash
docker exec -it postgres psql -U postgreRoot
```

### 停止数据库容器

```bash
docker compose stop
```

### 停止并删除数据库容器

```bash
docker compose down
```