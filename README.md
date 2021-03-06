##### Setup:

[Aster Carrier Board](https://www.toradex.com/products/carrier-boards/aster-carrier-board)

[Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/nxp-freescale-vybrid-vf6xx)

[Pioneer600](http://www.waveshare.com/wiki/Pioneer600)

<img src="https://github.com/bhuvanchandra/images-repo/blob/master/images-aster-pioneer600/aster-pioneer600.jpg" width="640" height="480">

Build and install the OE-Core aka Yocto SDK, for instructions related to
building and installing SDK, have a look at this knowledge base [article](http://developer.toradex.com/knowledge-base/linux-sdks).
After installing SDK, cross-compile libsoc.

###### Cross-compiling libsoc:
```
$ cd ~/
$ git clone https://github.com/jackmitch/libsoc.git
$ cd libsoc
$ . /usr/local/oecore-x86_64/environment-setup-armv7at2hf-neon-angstrom-linux-gnueabi
$ echo $CC
arm-angstrom-linux-gnueabi-gcc  -march=armv7-a -mthumb -mfpu=neon  -mfloat-abi=hard --sysroot=/usr/local/oecore-x86_64/sysroots/armv7at2hf-neon-angstrom-linux-gnueabi
$ autoreconf -i
$ ./configure --host=arm-angstrom-linux-gnueabi --prefix=/usr/local/oecore-x86_64/sysroots/armv7at2hf-neon-angstrom-linux-gnueabi/
...
$ make -j3
$ sudo make install
```
###### Cross-compiling libsoc examples:
```
$ cd ~/
$ git clone https://github.com/bhuvanchandra/libsoc-examples.git
$ cd libsoc-examples
$ cd ssd1306/
$ MACHINE=colibri-vf make
```
Copy the binary to target via ssh and run the example.
