# #548 - Can we create boost-config [Open]

> Username: sreerajkksd  
> Created at: 2021-08-25 10:55:18 UTC  
> Updated at: 2021-08-25 13:05:37 UTC  
> Url: https://github.com/boostorg/boost/issues/548  

Similar to many other packages (eg: curl, krb5, pcre, xml2, xslt etc..), can we also ship boost-config that would provide the installation options ?  
  
We can probably start with something like this ?  
  
```  
$ /usr/bin/boost-config --help  
Usage: boost-config [ options ]  
  
Options:  
  --cflags             print compiler options  
  --ldflags            print linker options  
  --prefix             print the boost prefix  
  --version            print the boost version  
  --help               print this usage summary  
  
$ /usr/bin/boost-config --version  
1.66.0  
$ /usr/bin/boost-config --cflags  
-I/usr/include  
$ /usr/bin/boost-config --ldflags  
-L/usr/lib64 -Wl,-R/usr/lib64  
```

---

## Comment 1

> Username: mclow  
> Created at: 2021-08-25 13:05:37 UTC  
> Url: https://github.com/boostorg/boost/issues/548#issuecomment-905484158  

It would probably be better to discuss this on the boost mailing list.  (boost@lists.boost.org)
