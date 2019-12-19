**configure: error: jpeglib.h not found**

	# err
	checking whether to enable JIS-mapped Japanese font support in GD... no
	If configure fails try --with-webp-dir=<DIR>
	configure: error: jpeglib.h not found.
	    
	# solve
	sudo curl -O http://www.ijg.org/files/jpegsrc.v9a.tar.gz
	sudo tar -axvf jpegsrc.v9a.tar.gz
	cd jpeg-9a/
	sudo ./configure --prefix=/usr/local/jpeg
	sudo make
	sudo make install


**configure: error: png.h not found**

	# err
	checking for jpeg_read_header in -ljpeg... yes
	configure: error: png.h not found.
	    
	# solve
	sudo wget http://download.sourceforge.net/libpng/libpng-1.6.19.tar.gz
	sudo tar -zxvf libpng-1.6.19.tar.gz
	sudo ./configure --prefix=/usr/local/png
	sudo make
	sudo make install

**configure: error: freetype-config not found.**

	# err
	If configure fails try --with-xpm-dir=<DIR>
	configure: error: freetype-config not found.
	    
	# solve1
	apt-get -y install libfreetype6-dev
	#solve2
	wget https://sourceforge.net/projects/freetype/files/freetype2/2.10.0/freetype-2.10.0.tar.bz2
	tar -xvf freetype-2.10.0
	cd freetype-2.10.0
	./configure --prefix=/usr/local/freetype
	sudo make
	sudo make install

**configure: error: Please reinstall the iconv library.**

	# err
	If configure fails try --with-xpm-dir=<DIR>
	configure: error: freetype-config not found.
	    
	# solve
	wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz
	tar -zxvf libiconv-1.14.tar.gz
	cd libiconv-1.14
	.\configure --prefix=/usr/local/iconv
	sudo make
	sudo make install

**编译安装libiconv报错：./stdio.h:1010:1: error: 'gets' undeclared here (not in a function)**

	# err
	In file included from progname.c:26:0:
	    ./stdio.h:1010:1: error: ‘gets’ undeclared here (not in a function)
	    _GL_WARN_ON_USE (gets, "gets is a security hole - use fgets instead");
	    ^
	    make[2]: *** [progname.o] Error 1
	    make[2]: Leaving directory `/usr/local/src/zabbix-2.4.7/libiconv-1.14/srclib'
	    make[1]: *** [all] Error 2
	    make[1]: Leaving directory `/usr/local/src/zabbix-2.4.7/libiconv-1.14/srclib'
	    make: *** [all] Error 2
	    
	# solve
	vi libiconv-1.14/srclib/stdio.in.h
	将698行的代码：_GL_WARN_ON_USE (gets, "gets is a security hole - use fgets instead");替换为：
	#if defined(__GLIBC__) && !defined(__UCLIBC__) && !__GLIBC_PREREQ(2, 16)
	 _GL_WARN_ON_USE (gets, "gets is a security hole - use fgets instead");
	#endif

**configure: error: Cannot find OpenSSL's <evp.h>**

	# err
	configure: error: Cannot find OpenSSL's <evp.h>
    
	# solve
	openssl-dev ,是redhat，centos 中的软件包，而debian和ubuntu 中为openssl
	若php扩展中已安装了openssl，则安装libssl-dev 资源包，未安装openssl，则先安装openssl
