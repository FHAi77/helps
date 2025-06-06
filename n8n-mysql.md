---
title: n8n 自然语言查询数据库
tags: Mysql,n8n
---

## 重要提醒
**请确保MySQL的版本要大于5.7.27**

## 宿主机地址
- host.docker.internal

## 创建MySQL连接凭证
- 宿主机地址 ```host.docker.internal```

## 查询数据库中所有表和表中的字段信息

```
SELECT 
    t.TABLE_NAME AS `tablename`,
    t.TABLE_COMMENT AS `tablecomment`,
    c.COLUMN_NAME AS `columnname`,
    c.COLUMN_COMMENT AS `columncomment`,
    c.COLUMN_TYPE AS `columntype`
FROM 
    information_schema.TABLES t
JOIN 
    information_schema.COLUMNS c 
    ON t.TABLE_SCHEMA = c.TABLE_SCHEMA AND t.TABLE_NAME = c.TABLE_NAME
WHERE 
    t.TABLE_SCHEMA = DATABASE()
ORDER BY 
    t.TABLE_NAME, c.ORDINAL_POSITION; -- 按表和字段顺序排序

```

### 将结果转为文本内容
```
const result = {};
items.forEach(item => {
  const tableName = item.json.tablename;

  // 如果该表名还没添加到 result，则初始化
  if (!result[tableName]) {
    result[tableName] = {
      tablename: tableName,
      tablecomment: item.json.tablecomment || "",
      fields: [] // 用于存储该表的所有字段
    };
  }

  // 添加字段信息
  result[tableName].fields.push({
    columnname: item.json.columnname,
    columncomment: item.json.columncomment || "",
    columntype: item.json.columntype || ""
  });
});

// 转换为数组返回
const tempRes= Object.values(result).map(table => ({ json: table }));
return [
  {
    json: {
      fileContent: tempRes.map(item => {
        let tableName = `# ${item.json.tablecomment} ${item.json.tablename}`;
        let fields = item.json.fields.map(field => `- ${field.columnname} ${field.columntype} ${field.columncomment}`).join("\n");
        return `${tableName}\n${fields}\n`;
      }).join("\n")
    }
  }
];
```

## AI Agent 生成sql的prompt
```
你是一个经验丰富的DBA，精通MySQL数据库。你会根据用户的需求和数据结构，生成一个查询语句。只需要返回一个可执行的SQL语句，不需要任何解释。
数据结构：
## 对话示例
用户：查询职工总人数
回答：select count(*) as totalworkers from zm_workers where history is null and state=1;
``` 

## 最终工作流json
```
{
  "name": "自然语言查询MySQL",
  "nodes": [
    {
      "parameters": {},
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [
        -1580,
        -360
      ],
      "id": "1c2d2fce-eea5-4234-8113-87fb765c1cc8",
      "name": "When clicking ‘Test workflow’"
    },
    {
      "parameters": {
        "operation": "executeQuery",
        "query": "SELECT \n    t.TABLE_NAME AS `tablename`,\n    t.TABLE_COMMENT AS `tablecomment`,\n    c.COLUMN_NAME AS `columnname`,\n    c.COLUMN_COMMENT AS `columncomment`,\n    c.COLUMN_TYPE AS `columntype`\nFROM \n    information_schema.TABLES t\nJOIN \n    information_schema.COLUMNS c \n    ON t.TABLE_SCHEMA = c.TABLE_SCHEMA AND t.TABLE_NAME = c.TABLE_NAME\nWHERE \n    t.TABLE_SCHEMA = DATABASE()\nORDER BY \n    t.TABLE_NAME, c.ORDINAL_POSITION; -- 按表和字段顺序排序",
        "options": {}
      },
      "type": "n8n-nodes-base.mySql",
      "typeVersion": 2.4,
      "position": [
        -1340,
        -360
      ],
      "id": "63ea7157-38a8-4659-a874-9b8802527c15",
      "name": "MySQL",
      "credentials": {
        "mySql": {
          "id": "cqr9Jh7LF7M2HEgG",
          "name": "MySQL连接凭证"
        }
      }
    },
    {
      "parameters": {
        "jsCode": "const result = {};\nitems.forEach(item => {\n  const tableName = item.json.tablename;\n\n  // 如果该表名还没添加到 result，则初始化\n  if (!result[tableName]) {\n    result[tableName] = {\n      tablename: tableName,\n      tablecomment: item.json.tablecomment || \"\",\n      fields: [] // 用于存储该表的所有字段\n    };\n  }\n\n  // 添加字段信息\n  result[tableName].fields.push({\n    columnname: item.json.columnname,\n    columncomment: item.json.columncomment || \"\",\n    columntype: item.json.columntype || \"\"\n  });\n});\n\n// 转换为数组返回\nconst tempRes= Object.values(result).map(table => ({ json: table }));\nreturn [\n  {\n    json: {\n      fileContent: tempRes.map(item => {\n        let tableName = `# ${item.json.tablecomment} ${item.json.tablename}`;\n        let fields = item.json.fields.map(field => `- ${field.columnname} ${field.columntype} ${field.columncomment}`).join(\"\\n\");\n        return `${tableName}\\n${fields}\\n`;\n      }).join(\"\\n\")\n    }\n  }\n];"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [
        -1140,
        -360
      ],
      "id": "376f9dd2-a779-4865-b53f-914c5e2081e5",
      "name": "Code"
    },
    {
      "parameters": {
        "operation": "toText",
        "sourceProperty": "fileContent",
        "options": {}
      },
      "type": "n8n-nodes-base.convertToFile",
      "typeVersion": 1.1,
      "position": [
        -920,
        -360
      ],
      "id": "011fb3b4-ecb3-419c-b473-4f998f21e02d",
      "name": "Convert to File"
    },
    {
      "parameters": {
        "operation": "write",
        "fileName": "/home/node/hrmshcema.txt",
        "options": {}
      },
      "type": "n8n-nodes-base.readWriteFile",
      "typeVersion": 1,
      "position": [
        -700,
        -360
      ],
      "id": "a105f1c7-2c2e-4e2d-bb46-36980091f600",
      "name": "Read/Write Files from Disk"
    },
    {
      "parameters": {
        "content": "## 生成数据结构文件\n** 请在数据库结构发生变化的时候执行这个工作流 **",
        "height": 320,
        "width": 1180,
        "color": 5
      },
      "type": "n8n-nodes-base.stickyNote",
      "typeVersion": 1,
      "position": [
        -1680,
        -500
      ],
      "id": "8d3f0c8f-c8f9-4b78-b9d6-07b2d5029876",
      "name": "Sticky Note"
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.chatTrigger",
      "typeVersion": 1.1,
      "position": [
        -1620,
        20
      ],
      "id": "6fcb11b4-cf03-42b7-94a5-7d64bb505df8",
      "name": "When chat message received",
      "webhookId": "aa9571f3-5576-4f00-8150-b65c5cabea33"
    },
    {
      "parameters": {
        "fileSelector": "/home/node/hrmshcema.txt",
        "options": {}
      },
      "type": "n8n-nodes-base.readWriteFile",
      "typeVersion": 1,
      "position": [
        -1400,
        20
      ],
      "id": "f2f88e45-65c1-4897-a441-bbed5592a95f",
      "name": "Read/Write Files from Disk1"
    },
    {
      "parameters": {
        "operation": "text",
        "options": {}
      },
      "type": "n8n-nodes-base.extractFromFile",
      "typeVersion": 1,
      "position": [
        -1180,
        20
      ],
      "id": "613e3c79-87bf-484c-9a2d-d80a37290b9b",
      "name": "Extract from File"
    },
    {
      "parameters": {
        "promptType": "define",
        "text": "={{ $('When chat message received').item.json.chatInput }}",
        "options": {
          "systemMessage": "=你是一个经验丰富的DBA，精通MySQL数据库。你会根据用户的需求和数据结构，生成一个查询语句。只需要返回一个可执行的SQL语句，不需要任何解释。\n数据结构：{{ $json.data }}\n## 对话示例\n用户：查询职工总人数\n回答：select count(*) as totalworkers from zm_workers where history is null and state=1;"
        }
      },
      "type": "@n8n/n8n-nodes-langchain.agent",
      "typeVersion": 1.7,
      "position": [
        -960,
        20
      ],
      "id": "784c1ce8-8d06-4d83-acf6-62adee455857",
      "name": "AI Agent"
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatDeepSeek",
      "typeVersion": 1,
      "position": [
        -1020,
        220
      ],
      "id": "ece860d1-8368-403e-b43e-761dfef82909",
      "name": "DeepSeek Chat Model",
      "credentials": {
        "deepSeekApi": {
          "id": "ZICWuqWxccFkm0kD",
          "name": "DeepSeek account"
        }
      }
    },
    {
      "parameters": {
        "operation": "executeQuery",
        "query": "{{ $json.output }}",
        "options": {}
      },
      "type": "n8n-nodes-base.mySql",
      "typeVersion": 2.4,
      "position": [
        -600,
        20
      ],
      "id": "c3a02b5a-a56c-405d-a657-fff27ffbbcd6",
      "name": "MySQL1",
      "credentials": {
        "mySql": {
          "id": "cqr9Jh7LF7M2HEgG",
          "name": "MySQL连接凭证"
        }
      }
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "n8n-nodes-base.convertToFile",
      "typeVersion": 1.1,
      "position": [
        -360,
        20
      ],
      "id": "ca7b119d-c69d-4174-8951-77a3486e1f29",
      "name": "Convert to File1"
    },
    {
      "parameters": {
        "operation": "write",
        "fileName": "=/home/node/selectResult.csv",
        "options": {}
      },
      "type": "n8n-nodes-base.readWriteFile",
      "typeVersion": 1,
      "position": [
        -140,
        20
      ],
      "id": "12e8607f-e7b1-44dd-a109-fc013c8e5714",
      "name": "Read/Write Files from Disk2"
    },
    {
      "parameters": {
        "content": "## 自然语言查询MySQL数据库",
        "height": 480,
        "width": 1860
      },
      "type": "n8n-nodes-base.stickyNote",
      "typeVersion": 1,
      "position": [
        -1680,
        -140
      ],
      "id": "58b555d1-a698-49f4-ba7d-b716a2b0d998",
      "name": "Sticky Note1"
    }
  ],
  "pinData": {},
  "connections": {
    "When clicking ‘Test workflow’": {
      "main": [
        [
          {
            "node": "MySQL",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "MySQL": {
      "main": [
        [
          {
            "node": "Code",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Code": {
      "main": [
        [
          {
            "node": "Convert to File",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Convert to File": {
      "main": [
        [
          {
            "node": "Read/Write Files from Disk",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "When chat message received": {
      "main": [
        [
          {
            "node": "Read/Write Files from Disk1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Read/Write Files from Disk1": {
      "main": [
        [
          {
            "node": "Extract from File",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Extract from File": {
      "main": [
        [
          {
            "node": "AI Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "DeepSeek Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "AI Agent",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "AI Agent": {
      "main": [
        [
          {
            "node": "MySQL1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "MySQL1": {
      "main": [
        [
          {
            "node": "Convert to File1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Convert to File1": {
      "main": [
        [
          {
            "node": "Read/Write Files from Disk2",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "0bef0e02-a98d-4fae-b769-7d37783498ee",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "d36ca678f8b66bb6f5a018613aa17402c64553960e4ec06e7eb47ef5ce25102f"
  },
  "id": "uIbPFnIcResZoxPG",
  "tags": []
}
```