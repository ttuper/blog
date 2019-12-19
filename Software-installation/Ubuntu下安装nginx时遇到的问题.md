今天在一台新的服务器上 准备安装nginx 一开始装的扩展什么的都很顺利 但是make的时候出了问题 我确定所有需要的扩展都已经安装好了，出现问题如下：

root@ubuntu:/home/softpackage/nginx-1.9.9# make

cc1: all warnings being treated as errors
objs/Makefile:458: recipe for target 'objs/src/core/ngx_murmurhash.o' failed
make[1]: *** [objs/src/core/ngx_murmurhash.o] Error 1
make[1]: Leaving directory '/nginx-1.9.9'
Makefile:8: recipe for target 'build' failed

make: *** [build] Error 2


解决办法：  
将对应的makefile文件夹中（如本文中在 /nginx-1.9.9/objs/Makefile） 找到 -Werror 并去掉 在重新make即可

查了-Werror意思之后 发现原来它要求GCC将所有的警告当成错误进行处理 所有导致错误输出 并不能进行下一步