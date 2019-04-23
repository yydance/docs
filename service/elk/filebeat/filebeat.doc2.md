filebeat.doc2

[TOC]
### 1.安装
官方下载: <https://www.elastic.co/downloads/past-releases>，选择合适的版本下载。  
这里选择rpm包，安装到`/data/app/filebeat`，修改模块路径，启动脚本，后续下载地址: <http://mirrors.cloudwise.com/jkb/packages/filebeat-6.3.2-x86_64.rpm>
### 2.配置文件
主配置文件: `etc/filebeat.yml`  
示例说明：
```
# ============ 模块配置 ============
filebeat.modules:
# ============ 输入源配置 ============
filebeat.inputs:
- type: log
  enabled: false
  paths:
    - /var/log/*.log
# ============ 注册文件 ============
filebeat.registry_file: ${path.data}/registry
# ============ 配置文件加载 ============
filebeat.config:
  inputs:
    enabled: false
    path: inputs.d/*.yml
    reload.enabled: true
    reload.period: 10s
  modules:
    enabled: true
    path: modules.d/*.yml
    reload.enabled: true
    reload.period: 10s
# ============ 输出源配置 ============
output.elasticsearch:
  enabled: true
  hosts: ["120.131.14.191:29200"]
  compression_level: 4
  protocol: "http"
  username: "elastic"
  password: "E1astIc@o4l6%0ps"
  worker: 3
  index: "filebeat-%{[beat.version]}-%{+yyyy.MM.dd}"
  indices:
    - index: "jkb-api-%{+yyyy.MM.dd}"
      when.contains:
        source: "/data/logs/httpd/api"
    - index: "jkb-plugin-%{+yyyy.MM.dd}"
      when.contains:
        source: "/data/logs/httpd/plugin"
  ssl.certificate_authorities: ["/data/app/filebeat/etc/certs/ca.crt"]
  ssl.certificate: "/data/app/filebeat/etc/certs/efk-es.cloudwise.com.crt"
  ssl.key: "/data/app/filebeat/etc/certs/efk-es.cloudwise.com.key"
# ============ 数据目录 ============
path.data: /data/appData/filebeat
# ============ 日志目录 ============
path.logs: /data/logs/filebeat
# ============ 模板设置 ============
setup.template.enabled: true
setup.template.name: "filebeat-%{[beat.version]}"
setup.template.pattern: "filebeat-%{[beat.version]}-*"
setup.template.overwrite: false
setup.template.settings:
# ============ kibana配置 ============
setup.kibana:
# ============ 日志配置 ============
logging.level: warning
logging.to_files: true
logging.files:
  path: /data/logs/filebeat
  name: filebeat
  keepfiles: 7
  permissions: 0600
```
默认模板字段配置文件: `etc/field.yml`

***注意：*** 如果配置了ssl证书，证书文件需要放在`${path.config}`目录下。

### 3.启动脚本
centos 6.x:  /etc/init.d/filebeat
```
#!/bin/bash
#
# filebeat          filebeat shipper
#
# chkconfig: 2345 98 02
# description: Starts and stops a single filebeat instance on this system
#
### BEGIN INIT INFO
# Provides:          filebeat
# Required-Start:    $local_fs $network $syslog
# Required-Stop:     $local_fs $network $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Filebeat sends log files to Logstash or directly to Elasticsearch.
# Description:       filebeat is a shipper part of the Elastic Beats
#					 family. Please see: https://www.elastic.co/products/beats
### END INIT INFO

PATH=/usr/bin:/sbin:/bin:/usr/sbin
export PATH
[ -f /etc/sysconfig/filebeat ] && . /etc/sysconfig/filebeat
pidfile=${PIDFILE-/data/pid/filebeat.pid}
agent=${BEATS_AGENT-/data/app/filebeat/bin/filebeat}
args="-c /data/app/filebeat/etc/filebeat.yml -path.home /data/app/filebeat -path.config /data/app/filebeat/etc"
test_args="-e test config"
beat_user="${BEAT_USER:-root}"
wrapper="/data/app/filebeat/bin/filebeat-god"
wrapperopts="-r / -n -p $pidfile"
user_wrapper="su"
user_wrapperopts="$beat_user -c"
RETVAL=0

# Source function library.
. /etc/rc.d/init.d/functions

# Determine if we can use the -p option to daemon, killproc, and status.
# RHEL < 5 can't.
if status | grep -q -- '-p' 2>/dev/null; then
    daemonopts="--pidfile $pidfile"
    pidopts="-p $pidfile"
fi

if command -v runuser >/dev/null 2>&1; then
    user_wrapper="runuser"
fi

[ "$beat_user" != "root" ] && wrapperopts="$wrapperopts -u $beat_user"

test() {
	$user_wrapper $user_wrapperopts "$agent $args $test_args"
}

start() {
    echo -n $"Starting filebeat: "
	test
	if [ $? -ne 0 ]; then
		echo
		exit 1
	fi
    daemon $daemonopts $wrapper $wrapperopts -- $agent $args
    RETVAL=$?
    echo
    return $RETVAL
}

stop() {
    echo -n $"Stopping filebeat: "
    killproc $pidopts $wrapper
    RETVAL=$?
    echo
    [ $RETVAL = 0 ] && rm -f ${pidfile}
}

restart() {
	test
	if [ $? -ne 0 ]; then
		return 1
	fi
    stop
    start
}

rh_status() {
    status $pidopts $wrapper
    RETVAL=$?
    return $RETVAL
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

case "$1" in
    start)
        start
    ;;
    stop)
        stop
    ;;
    restart)
        restart
    ;;
    condrestart|try-restart)
        rh_status_q || exit 0
        restart
    ;;
    status)
        rh_status
    ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart}"
        exit 1
esac
exit $RETVAL
```
centos 7.x: /usr/lib/systemd/system/filebeat.service
 
 ```
 [Unit]
Description=filebeat
Documentation=https://www.elastic.co/guide/en/beats/filebeat/current/index.html
Wants=network-online.target
After=network-online.target

[Service]
ExecStart=/data/app/filebeat/bin/filebeat -c /data/app/filebeat/etc/filebeat.yml -path.home /data/app/filebeat -path.config /data/app/filebeat/etc -path.data /data/appData/filebeat -path.logs /data/logs/filebeat
Restart=always

[Install]
WantedBy=multi-user.target
 ```
 