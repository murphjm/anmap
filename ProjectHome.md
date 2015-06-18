<font color='gray'> <h1> Android Network Mapper (network scanner original nmap for android)</h1> </font>


Prerelease [anmap.apk](http://anmap.googlecode.com/files/anmap.apk) done.

There are some little bugs, as domain names resolving and unroot scanning.

Nmap data files is also too big for ordinary device memory, 5mb. In the future it would be moved to external memory. Application should be about 2mb.

The same way you may download compiled nmap with data files in this [tar ball](http://anmap.googlecode.com/files/nmap-5.51-1.tar) or this [zip archive](http://anmap.googlecode.com/files/nmap-5.51-1.zip) and keep it in one folder.

Than connect to localhost via [ConnectBot](http://code.google.com/p/connectbot/), untar and start scanning:

```
$ tar -xvf nmap-5.51-1.tar
$ su 
# ./nmap 127.0.0.1
```

You probably necessary root to make all kinds of scan. But some of them you can get like a user.

[How-to make apk from original nmap tar.gz(sketch)](http://code.google.com/p/anmap/wiki/Build)