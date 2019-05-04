部署框架:nginx+gunicorn+django,其中gunicorn由supervisor管理

当部署完nginx后一直是400，怎么会呢？

原来，默认django配置的DEBUG为true,服务器不会检查request header中的HTTP_HOST;这里DEBUG设置为False，就会检查。经过Nginx做反向代理，并没有设置转发后的request的host。

解决：配置nginx的proxy_set_header
```
proxy_set_header HOST $host;
```
