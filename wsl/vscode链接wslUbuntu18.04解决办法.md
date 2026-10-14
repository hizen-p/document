先使用vscode的远程资源管理器链接WSL的ubuntu18（正常会报错，目的是在home目录下产生vscode的文件）

![78680671405](C:\Users\hzen0\AppData\Local\Temp\1786806714058.png)

```
sudo apt-get update
sudo apt-get install bison
sudo apt-get install patchelf

mkdir ~/src  
cd ~/src

wget 'https://ftp.gnu.org/gnu/glibc/glibc-2.28.tar.gz'
tar xzf glibc-2.28.tar.gz

mkdir glibc-2.28-build
cd glibc-2.28-build
../glibc-2.28/configure --prefix=/opt/glibc-2.28



make -j$(nproc)

sudo make install


cd ~/.vscode-server/bin/a5b500951314efd502d07465bd138dfbd714a960(自己的vscode哈希值)
cp node node_bak
patchelf --set-interpreter /opt/glibc-2.28/lib/ld-linux-x86-64.so.2 --set-rpath /opt/glibc-2.28/lib:/lib/x86_64-linux-gnu:/usr/lib/x86_64-linux-gnu node
```

![78680669737](C:\Users\hzen0\AppData\Local\Temp\1786806697378.png)