使用devpi 作为pip 缓存代理服务器

安装：
```
pip install devpi-server
```  

启动devpi-server:
```
devpi-server --host=0.0.0.0 --start 
```  

默认缓存服务器使用3141端口
使用缓存代理：
```
pip install -i http://localhost:3141/root/pypi/ tornado
```  
将"index-url"设置写到配置文件中：
```
mkdir -p ~/.config/pip
cat ~/.config/pip/pip.conf
[global]
index-url = http://localhost:3141/root/pypi/+sample/
```  

devpi web界面：
```
pip install -U devpi-web
```  

重启devpi-server:
```
devpi-server --host=0.0.0.0 --stop 
devpi-server --host=0.0.0.0 --start 
```  

通过http://127.0.0.1:3141/访问web界面
