日志分析系统，常用架构:  
- logstash->es->kibana
- logstash->redis->es->kibana
- filebeat->es->kibana
- filebeat->kafka->logstash->kibana

本示例为filebeat->es->kibana
