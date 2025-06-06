---
title: 【0基础系列01】安装docker和n8n
tags: Linux,centos,docker,n8n
---


## 添加docker官方源到yum仓库

```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
## 安装docker主程序命令行和依赖组件

```
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

### 把docker加入开机启动

```
sudo systemctl enable docker
```

### 启动docker

```
sudo systemctl start docker
```

关闭docker `sudo systemctl stop docker`

重新启动docker `sudo systemctl restart docker`


## 安装n8n

### 创建文件夹 指令格式
```
mkdir 文件夹名称
```
### 创建文件夹和子文件夹 指令格式
```
mkdir -p 上级文件夹/{下级文件夹1,下级文件夹2}
```

### 创建n8n的数据保存目录

```
mkdir -p dockers-data/n8ndata/n8n
```

### 进入n8ndata目录
```
cd dockers-data/n8ndata
```

### 将目录权限交给n8n容器
```
sudo chown -R 1000:1000 ./n8n
```


## 创建docker-compose.yml文件

```
vim docker-compose.yml
```

## docker-compose文件内容
```
services:
  n8n:
    image: n8nio/n8n
    ports:
      - 5678:5678
    volumes:
      - ./n8n:/home/node/.n8n
    restart: always
    environment:
      - N8N_SECURE_COOKIE=false

```

## 启动n8n容器
```
docker compose up -d
```

## n8n访问地址
```
http://你的服务器IP:5678
```