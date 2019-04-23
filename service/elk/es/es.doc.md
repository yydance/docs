es.doc

[TOC]
### 1.数据存储

### 2.安装

### 3.配置文件

### 4.启动脚本

### 5.激活破解x-pack
es下载地址：<http://mirrors.cloudwise.com/jkb/packages/es-6.3.2-linux.tar.gz>  
其中x-pack单独的核心jar包(`x-pack-core-6.3.2.jar`)已破解,下载地址：<http://mirrors.cloudwise.com/jkb/packages/x-pack-core-6.3.2.jar>,该包在es-6.3.0和es-6.3.2版本验证可用。

---  
激活x-pack试用版
```
curl -u elastic:E1astIc@o4l6%0ps -XPUT 'http://localhost:29200/_xpack/license' -H "Content-Type:application/json" -d @license.json
```
license.json
```
{"license":{"uid":"de2d8478-7454-4b2c-b42b-6c86dfda19d7","type":"platinum","issue_date_in_millis":1536537600000,"expiry_date_in_millis":4102329600000,"max_nodes":500,"issued_to":"damon yang (cloudwise)","issuer":"Web Form","signature":"AAAAAwAAAA0DFbYqvzSk61251kQ0AAABmC9ZN0hjZDBGYnVyRXpCOW5Bb3FjZDAxOWpSbTVoMVZwUzRxVk1PSmkxaktJRVl5MUYvUWh3bHZVUTllbXNPbzBUemtnbWpBbmlWRmRZb25KNFlBR2x0TXc2K2p1Y1VtMG1UQU9TRGZVSGRwaEJGUjE3bXd3LzRqZ05iLzRteWFNekdxRGpIYlFwYkJiNUs0U1hTVlJKNVlXekMrSlVUdFIvV0FNeWdOYnlESDc3MWhlY3hSQmdKSjJ2ZTcvYlBFOHhPQlV3ZHdDQ0tHcG5uOElCaDJ4K1hob29xSG85N0kvTWV3THhlQk9NL01VMFRjNDZpZEVXeUtUMXIyMlIveFpJUkk2WUdveEZaME9XWitGUi9WNTZVQW1FMG1DenhZU0ZmeXlZakVEMjZFT2NvOWxpZGlqVmlHNC8rWVVUYzMwRGVySHpIdURzKzFiRDl4TmM1TUp2VTBOUlJZUlAyV0ZVL2kvVk10L0NsbXNFYVZwT3NSU082dFNNa2prQ0ZsclZ4NTltbU1CVE5lR09Bck93V2J1Y3c9PQAAAQALfEn1CU2s31wgbqzj5PwX0ct/VsI/NrdFXylK1Uf6ADsIGXlqOD/U1QKOe1Cp2hl+kwTx92ZSHjGUQsFyqEemtSq2vEwiLcxNqeRkz7yp884FkWJx9lWOt7lcpZ/3gIj3GOSO8YZMpaQN2RK2DrbWrq1C20IZHQadtRsOPKScamPd/NO8UflbdeBi+AI0ApaDUqWECHrFFIiqkY1C73a+pp0J3v9qMiiTzZFR7Wp4rotGkTTcIicTrSDyBMbtiyt797nFGt9onhdqqlN+sNL1Fo3/EXBWFTLjIfEuANhH/Z5ElCTgts8ab1l5ndc5xUvFLjLwlAmC61nYQUzU+KPK","start_date_in_millis":1536537600000}}
```

### 6.按照日期删除索引
官方：[curator](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/index.html)  
配置样例  
* 安装curator,这里使用rpm包，安装到/opt/elasticsearch-curator
* 配置文件  
${curator_home}/config/config.yml：

```
---
# Remember, leave a key empty if there is no value.  None will be a string,
# not a Python "NoneType"
client:
  hosts:
    - 10.100.0.19
    - 10.100.0.13
  port: 29200
  url_prefix:
  use_ssl: False
  certificate: '/opt/elasticsearch-curator/certs/ca.crt'
  client_cert: '/opt/elasticsearch-curator/certs/efk-es.cloudwise.com.crt'
  client_key:  '/opt/elasticsearch-curator/certs/efk-es.cloudwise.com.key'
  ssl_no_validate: True
  http_auth: elastic:E1astIc@o4l6%0ps
  timeout: 30
  master_only: False

logging:
  loglevel: INFO
  logfile: '/data/logs/es-curator.log'
  logformat: default
  #blacklist: ['elasticsearch', 'urllib3']
```
${curator_home}/config/action.yml

```
---
actions:
  1:
    action: delete_indices
    description: >-
      Delete indices older than 7 days (based on index name), for logstash-
      prefixed indices. Ignore the error if the filter does not result in an
      actionable list of indices (ignore_empty_list) and exit cleanly.
    options:
      ignore_empty_list: True
      timeout_override:
      continue_if_exception: False
      disable_action: False
    filters:
    - filtertype: pattern
      kind: timestring
      value: '%Y.%m.%d'
      exclude:
    - filtertype: age
      source: name
      direction: older
      timestring: '%Y.%m.%d'
      unit: days
      unit_count: 7
      exclude:
```
* 配置cron脚本
/data/app/script/cron/curator.sh

```
#!/bin/bash

curator_home="/opt/elasticsearch-curator"
curator_bin="$curator_home/curator"
config_file="$curator_home/config/config.yml"
action_file="$curator_home/config/action.yml"

$curator_bin --config $config_file [--dry-run] $action_file
```
--dry-run：该参数只是输出可删除的索引，并不真正执行。