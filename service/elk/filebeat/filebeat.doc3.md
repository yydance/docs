filebeat.doc3

### 1. 解码json字段
官方示例：
```
processors:
 - decode_json_fields:
     fields: ["field1", "field2", ...]
     process_array: false
     max_depth: 1
     target: ""
     overwrite_keys: false
```
说明：  
* `fields`: 需要解码json的字段
* `process_array`: 是否处理数组，默认false
* `max_depth`: 解码深度，默认为1

nginx json日志示例：
```
- type: log
  enabled: true
  json.keys_under_root: true
  tags: ['qiye']
  processors:
    - decode_json_fields:
        fields: ['message']
  paths:
    - /data/logs/nginx/www.jiankongbao.com/jkb-qiye.access.log
  exclude_files: ['.gz$']
```
### 2. 修改自带模块字段
**2.1 修改自带模块字段，包含三部分：**  
* 日志处理管道,即 `ingest pipelie`
* 模板字段
* 模板  

> 正常情况下，修改日志处理管道文件和模板字段即可。

**2.2 以Apache为例说明**
* 处理管道文件，${path.home}/module/apache2/access/ingest/default.json
 默认pipeline修改后，需要手动删除es中pipeline，再生成新的pipeline，可修改配置文件`filebeat.yml`:  
  
 ```
 filebeat.overwrite_pipelines: true
 ```
 配置不同的`pipeline`示例:
 ```
 pipelines:
    - pipeline: "warning_pipeline"
      when.contains:
        message: "WARN"
    - pipeline: "error_pipeline"
      when.contains:
        message: "ERR"
 ```
* 模板字段文件，`${path.config}/fields.yml`
附：[字段类型](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/mapping-types.html)

* 模板设置  
  `filebeat`配置文件中默认模板为`filebeat-%{[beat.version]}`，修改模板变量：  
  ```
  setup.template.enabled: true
setup.template.name: "custom"
setup.template.pattern: "custom-*"
setup.template.fields: "/etc/filebeat/fields.yml" ## 生成模板的字段配置文件
setup.template.overwrite: true ## 如果不设置为true，则需要手动先删除模板，再创建模板
  ```
  