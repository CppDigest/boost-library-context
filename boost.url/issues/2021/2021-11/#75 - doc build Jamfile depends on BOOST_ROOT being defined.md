# #75 - doc build Jamfile depends on BOOST_ROOT being defined [Closed]

> Username: evanlenz  
> Created at: 2021-11-01 01:55:14 UTC  
> Updated at: 2021-11-02 01:28:13 UTC  
> Closed at: 2021-11-02 01:28:13 UTC  
> Url: https://github.com/boostorg/url/issues/75  

I am getting the following error when I run the doc build.  
  
```  
evanl@THINKPAD-T480 ~/boost/libs/url/doc  
$ b2  
Jamfile:101: in modules.load  
*** argument error  
* rule glob ( wildcards + : excludes * )  
* called with: (  )  
* missing argument wildcards  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:1274:see definition of rule 'glob' being called  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:372: in load-jamfile  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:64: in load  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:142: in project.find  
C:/cygwin64/home/evanl/boost/tools/build/src\build-system.jam:618: in load  
C:/cygwin64/home/evanl/boost/tools/build/src/kernel\modules.jam:295: in import  
C:/cygwin64/home/evanl/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
C:/cygwin64/home/evanl/boost/boost-build.jam:17: in module scope  
```  
  
I was able to run it successfully before [this change](https://github.com/CPPAlliance/url/commit/ac8b187391aa33c89d5f61f72aec762dc5ce7650).  
  
It looks like it is expecting BOOST_ROOT to be defined (and I was able to get it to work by manually adding that to my environment). However, I know that @alandefreitas also ran into this problem, and none of the other libraries that use docca have this problem. Can the Jamfile be written without this dependency as in the other projects?

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-11-01 01:56:42 UTC  
> Url: https://github.com/boostorg/url/issues/75#issuecomment-955860128  

@sdarwin Can the Jamfile be written without this dependency as in the other projects that use docca? It's not a problem for me now that I figured it out, but I expect that others will run into it.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-11-01 02:01:30 UTC  
> Url: https://github.com/boostorg/url/issues/75#issuecomment-955862003  

Replacing  
```  
local broot = [ os.environ BOOST_ROOT ] ;  
```  
  
with  
  
```  
path-constant broot : ../../.. ;  
```  
  
seems to work.
