屏蔽NGINX、Apache、PHP版本号

### 屏蔽NGINX，修改源码
1.nginx/src/http/ngx_http_header_filter_module.c  
```
找到static char ngx_http_server_string[] = "Server: nginx" CRLF;  这一行，修改里面的Server:nginx为你想要的名称，如:Server:xws
```  

2.nginx/src/core/nginx.h
```
找到如下几行并按照自己的意愿修改，如：
#define nginx_version         0110
#define NGINX_VERSION      "0.1.10"
#define NGINX_VER          "xws/" NGINX_VERSION
#define NGINX_VAR          "XWS"
```  

3.编译安装后，修改配置文件  
nginx.conf  
```
server_tokens off;
```  

fastcgi.conf/fcgi.conf
```
fastcgi_param SERVER_SOFTWARE nginx/$nginx_version
->
fastcgi_param SERVER_SOFTWARE nginx;
```

### 屏蔽Apache，修改源码
两个源文件, `httpd-2.4.26/include/ap_release.h` and `httpd-2.4.26/os/unix/os.h`

httpd-2.4.26/include/ap_release.h
```
#define AP_SERVER_COPYRIGHT \
  "Copyright 2019 by cloudwise."
#define AP_SERVER_BASEENDOR "cloudwise technology company"
#define AP_SERVER_BASEPROJECT "web server"
#define AP_SERVER_BASEPRODUCT "windows"
```  

httpd-2.4.26/os/unix/os.h
```
#ifndef PLATFORM
#define PLATFORM "Win32"
#endif
```

编译安装完成，修改httpd.conf
```
ServerTokens ProductOnly
ServerSignature Off
```  

### 屏蔽PHP，修改源码
main/main.c
```
SAPI_PHP_VERSION_HEADER
删除外面的if块，共2块
```  

编译安装完成，修改配置文件php.ini
```
expose_php = Off
```
