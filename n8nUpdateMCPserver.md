# n8n版本更新和MCP服务器应用

## n8n版本更新

- 执行如下指令查看当前运行容器的信息
```
docker ps
```

-- 执行备份命令，备份n8n数据
```
docker cp n8n:/home/node/.n8n ./docker-n8ndatas
```
> 命令翻译：将n8n容器中的/home/node/.n8n目录复制到当前目录下的docker-n8ndatas目录

在docker desktop中，停止删除n8n容器，并在镜像列表中删除镜像

- 使用如下命令运行新的容器
```
docker run -d   --name n8n   -p 5678:5678   -v G:/docker-n8ndata/.n8n:/home/node/.n8n n8nio/n8n:1.88.0
```
> 命令翻译：运行一个名为n8n的容器，将5678端口映射到主机，将G:/docker-n8ndata/.n8n目录挂载到容器中的/home/node/.n8n目录，使用n8nio/n8n:1.88.0镜像

- 访问docker 中的n8n
```
[http://localhost:5678](http://localhost:5678)  
```

## 创建MCP服务器

- 天气查询api申请点击这里：openweather [https://openweathermap.org/price](https://openweathermap.org/price)

## MCP 服务器应用

- Gemini API 密钥申请点击这里：gemini [https://aistudio.google.com/](https://aistudio.google.com/)

- 宿主机地址 ```host.docker.internal```


