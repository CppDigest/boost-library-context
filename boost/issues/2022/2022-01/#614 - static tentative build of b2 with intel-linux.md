# #614 - static tentative build of b2 with intel-linux [Closed]

> Username: aminiussi  
> Created at: 2022-01-31 20:59:46 UTC  
> Updated at: 2022-01-31 21:10:34 UTC  
> Closed at: 2022-01-31 21:10:19 UTC  
> Url: https://github.com/boostorg/boost/issues/614  

Hi,   
  
I just tried to build boost out of a fresh clone and got:  
```  
21:43:20 [alainm@castor boost]# ./bootstrap.sh --with-toolset=intel-linux  
Building B2 engine..  
...  
 icpx -x c++ -std=c++11 -O3 -s -static -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp execunix.cpp filesys.cpp filent.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam_strings.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp -o b2  
ld: cannot find -lstdc++  
ld: cannot find -lstdc++  
> cp b2 bjam  
cp: cannot stat ‘b2’: No such file or directory  
  
Failed to build B2 build engine  
21:43:38 [alainm@castor boost]#  
```  
I have 2 question:  
 1. why icpx instead of icpc ?  
 2. why the -static option ? it will try to get the archive version of libstdc++ and such a thing does not exists (at least I could not find it).  
   
As a side note, the -s is used, bit according to the doc it does not exist.  
  
Thanks

---

## Comment 1

> Username: aminiussi  
> Created at: 2022-01-31 21:10:34 UTC  
> Url: https://github.com/boostorg/boost/issues/614#issuecomment-1026212910  

Wrong submodule
