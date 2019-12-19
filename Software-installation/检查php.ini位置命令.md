如果再编译php时，没有指定配置文件位置，即没有指定 `--with-config-file-path=/usr/local/php/etc`，则默认情况下，php.ini的位置就会被指向php的lib目录  
可以通过如下命令查看当前配置文件的生效目录：  
`php -i | grep php.ini`

php -i 的意思就是列出php的详细信息，然后通过管道命令筛选出php.ini的信息