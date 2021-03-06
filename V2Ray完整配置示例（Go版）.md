## V2Ray Go版 完整配置示例（类型：TCP）

#### 校准节点系统时间
```
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
yum install ntpdate
ntpdate -u time.nist.gov
```

#### 面板里添加一个V2Ray的节点
[![1.png](https://i.loli.net/2019/01/07/5c3343be53678.png)](https://i.loli.net/2019/01/07/5c3343be53678.png)

#### 安装SSRPanel的V2Ray Go版插件
```
curl -L -s https://raw.githubusercontent.com/ColetteContreras/v2ray-ssrpanel-plugin/master/install-release.sh | sudo bash
```
#### 配置V2Ray
`vim /etc/v2ray/config.json` 

然后将如下配置黏贴进去，修改，然后`删除所有//开头的注释(注意//前有一个空格也要删除)`并保存
```
{
  "log": {
    "loglevel": "debug"
  },
  "api": {
    "tag": "api",
    "services": [
      "HandlerService",
      "LoggerService",
      "StatsService"
    ]
  },
  "stats": {},
  "inbounds": [
    {
      "port": 10086,
      "protocol": "vmess",
      "tag": "proxy"
    },
    {
      "listen": "127.0.0.1",
      "port": 10085,
      "protocol": "dokodemo-door",
      "settings": {
        "address": "127.0.0.1"
      },
      "tag": "api"
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom"
    }
  ],
  "routing": {
    "rules": [
      {
        "type": "field",
        "inboundTag": [
          "api"
        ],
        "outboundTag": "api"
      }
    ],
    "strategy": "rules"
  },
  "policy": {
    "levels": {
      "0": {
        "statsUserUplink": true,
        "statsUserDownlink": true
      }
    },
    "system": {
      "statsInboundUplink": true,
      "statsInboundDownlink": true
    }
  },
  "ssrpanel": {
    "nodeId": 1, //面板里添加完节点后生成的自增ID
    "checkRate": 60,
    "user": {
      "inboundTag": "proxy",
      "level": 0,
      "alterId": 16, //必须跟面板里设定的alterId一致
      "security": "none"
    },
    "mysql": {
      "host": "144.33.X.X", //数据库所在地址，建议填IP
      "port": 3306,
      "user": "ssrpanel", //数据库连接账号
      "password": "password", //数据库连接密码
      "dbname": "ssrpanel" //数据库名称
    }
  }
}
```

##### 测试运行
```
/usr/bin/v2ray/v2ray -config /etc/v2ray/config.json
```

#### 客户端配置
[![2.png](https://i.loli.net/2019/01/07/5c3343be6fd4a.png)](https://i.loli.net/2019/01/07/5c3343be6fd4a.png)

##### 后台运行
```
touch v2ray_logrun.sh
vim v2ray_logrun.sh

黏贴如下

nohup /usr/bin/v2ray/v2ray -config /etc/v2ray/config.json >> v2ray_run.log 2>&1 &

保存并执行： chmod a+x v2ray_logrun.sh && sh v2ray_logrun.sh
```