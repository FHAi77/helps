# n8n MCP客户端案例指南

## 下载启动n8n镜像
- 设置环境变量名
```
N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE
```
- 设置环境变量值
```
true
```

## 安装n8n MCP客户端节点
```
n8n-nodes-mcp
```

## 创建[Brave Search MCP](https://github.com/modelcontextprotocol/servers/tree/main/src/brave-search) 链接凭证配置

- command 安装指令
```
npx
```
- arguments 安装参数
```
-y @modelcontextprotocol/server-brave-search
```
- Environments 环境变量，切换到 Expression 表达式模式
```
BRAVE_API_KEY=你的Brave API密钥
```

申请一个免费的Brave API密钥：[点击这里](https://brave.com/search/api/)


- 执行工具节点的配置指令
```
{{ $fromAI('tool','select tool to execute') }}
```

## 创建[文件处理](https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem)节点凭证

- command 安装指令
```
npx
```
- arguments 安装参数
```
-y @modelcontextprotocol/server-filesystem /home/node/.n8n
```

## MCP样例服务器列表
```
https://modelcontextprotocol.io/examples
```