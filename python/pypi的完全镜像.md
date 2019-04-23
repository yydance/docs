安装bandersnatch
```
pip install -r https://bitbucket.org/pypa/bandersnatch/raw/stable/requirements.txt
```  

创建仓库目录：
```
mkdir -p /data/python/pypi
```  

同步官方仓库：
```
bandersnatch mirror
```  

修改配置文件，默认没有
```
vim /etc/bandersnatch.conf
master = https://pypi.python.org  #Pypi源
workers = 10 #启用多线程
directory = /data/python/pypi #设置仓库目录
```  

再次同步镜像，镜像比较大
```
bandersnatch mirror
```  

使用自定义Pypi源：
方法一：
修改配置文件
```
mkdir $HOME/.pip
vim $HOME/.pip/pip.conf
[global]
format=columns
index-url=http://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com
```  

方法二：
```
pip install $PACKAGE -i http://pypi.douban.com/simple
```  

加速：
由于bandersnatch默认是和官方的pypi源进行同步，国内访问速度和稳定性欠佳，试着修改该源，发现bandersnatch使用xml rpc方式向配置文件中定义的master服务器地址获取包列表信息的，而国内pypi源普遍只提供http方式下载，而没有提供相应的xml rpc服务。
因此，通过修改配置文件中的master源的方法看来是不行了。那就尝试一下能不能在下载环节进行加速，包列表还是向官网获取，下载地址指向国内地址也行，毕竟，同步的大部分时间是花在下载过程中的。
经过对比发现，豆瓣网的pypi源存储地址和官方源的结构是一样的只是域名部分的差别，让从官方下载的包，改为从豆瓣网下载。  
试着从bandersnatch源码入手看看能不能解决这个问题。  
发现bandersnatch源码的master.py第39行get函数是作用下载文件:  
```
def get(self, path, required_serial, **kw):
        logger.debug('Getting {0} (serial {1})'.format(path, required_serial))
        if not path.startswith(self.url):
            path = self.url + path
        # here, path is already https://pypi.python.org/packages/ ...
        r = self.session.get(path, timeout=self.timeout, **kw)
```  

该函数被多个地方调用，因此，复制该函数，再建立一个cn_get函数，修改该函数，替换包下载地址的域名部分，实现从douban下载。修改后的部分源码为：
```
def cn_get(self, path, required_serial, **kw):
        logger.debug('Getting {0} (serial {1})'.format(path, required_serial))
        if not path.startswith(self.url):
            path = self.url + path
        cn_url = "http://pypi.douban.com"
        cn_path = path.replace(self.url, cn_url)
        # here, path is already https://pypi.python.org/packages/ ...
        r = self.session.get(cn_path, timeout=self.timeout, **kw)
```  

在package.py的197行附近，修改调用master.get方法为cn_get。
```
r = self.mirror.master.cn_get(url, required_serial=None, stream=True)
        checksum = hashlib.md5()
```  

重新同步镜像：
```
bandersnatch mirror
```
