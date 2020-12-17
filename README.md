# checkinstall4centos7
对checkinstall-1.6.2源码进行修改，使之可以在centos7上正常使用

完整教程见[此处](https://lbbxsxlz.github.io/2020/10/13/checkinstall-centos/)

checkinstall-1.6.2源码下载，
```
git clone http://checkinstall.izto.org/checkinstall.git
```

或者

```
wget https://asic-linux.com.mx/~izto/checkinstall/files/source/checkinstall-1.6.2.tar.gz
```

## install
make;make install即可

## centos7中使用checkinstall制作rpm包

首先，修改/usr/local/lib/checkinstall/checkinstallrc文件
```
INSTYPE=""
```
	改成
```
INSTYPE="R"
```

其次，修复installwatch.so的路径问题
在checkinstall生成rpm包的过程中，报错内容为：

　　ERROR: ld.so: object '/usr/local/lib64/installwatch.so' from LD_PRELOAD cannot be preloaded: ignored.

通过以下命令解决

```
echo "/usr/local/lib64" >/etc/ld.so.conf.d/installwatch.conf
ln -s /usr/local/lib/installwatch.so /usr/local/lib64/installwatch.so
ldconfig
```

再次，使用hello程序包验证checkinstall工具
下载hello测试包,然后使用checkinstall生成RPM包

```
wget http://ftp.gnu.org/gnu/hello/hello-2.10.tar.gz
tar zxvf hello-2.10.tar.gz
cd hello-2.10;./configure;make
checkinstall -y  --backup=no --install=no --pkgname=hello --pkgversion=1.0 --pkgrelease=`date +"%Y%m%d%H"`  --pkggroup=IPDGroup --maintainer=lbbxsxlz@gmail.com --provides=Intellif --pakdir .
```

在以上的build package工程中会报以下错误，
/root/rpmbuild has no SOURCES directory. Please write the path to
the RPM source directory tree:

可以用以下命令修复问题


```
mkdir -p /root/rpmbuild/SOURCES
```

至此，用checkinstall生成RPM包圆满成功。
