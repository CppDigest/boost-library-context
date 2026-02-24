# #205 fix regression from VS2017 detection [Merged]

> Username: refack  
> Created at: 2017-06-27 14:46:37 UTC  
> Updated at: 2021-10-02 22:18:34 UTC  
> Merged at: 2017-06-27 15:16:57 UTC  
> Closed at: 2017-06-27 15:16:57 UTC  
> Url: https://github.com/boostorg/build/pull/205  



---

## Comment 1

> Username: refack  
> Created_at: 2017-06-27 15:19:20 UTC  
> Url: https://github.com/boostorg/build/pull/205#issuecomment-311391942  

Seems much better:  
```  
Build started  
git clone -q https://github.com/boostorg/build.git C:\projects\build  
git fetch -q origin +refs/pull/205/merge:  
git checkout -qf FETCH_HEAD  
cd src/engine  
build.bat  
###  
### Using 'vc12' toolset.  
###  
C:\projects\build\src\engine>if exist bootstrap rd /S /Q bootstrap   
C:\projects\build\src\engine>md bootstrap   
C:\projects\build\src\engine>cl /nologo /RTC1 /Zi /MTd /Fobootstrap/ /Fdbootstrap/ -DNT -DYYDEBUG -wd4996 kernel32.lib advapi32.lib user32.lib /Febootstrap\jam0  command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c   
command.c  
compile.c  
constants.c  
debug.c  
```

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2017-06-27 15:21:59 UTC  
> Url: https://github.com/boostorg/build/pull/205#issuecomment-311392802  

That looked fine before also.. The real test will be when this runs.. https://ci.appveyor.com/project/boostorg/boost/history

---
