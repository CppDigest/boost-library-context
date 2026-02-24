# #613 - Boost compilation fails with too many input files on AIX7.2 [Closed]

> Username: bergen288  
> Created at: 2020-06-01 20:54:40 UTC  
> Updated at: 2023-06-15 13:03:39 UTC  
> Closed at: 2020-06-15 20:44:56 UTC  
> Url: https://github.com/boostorg/build/issues/613  

I am trying to compile boost_1_73_0 on my AIX7.2 server. The default compiler is gcc8.3.0. However, bootstrap.sh says gcc doesn't support C++11 while it also gives list of toolsets including gcc.  See attached bootstrap.log.  
[bootstrap.log](https://github.com/boostorg/boost/files/4713375/bootstrap.log)  
  
If I run build.sh inside tools/build/src/engine, it won't complain about gcc, but it gives me "cpp: fatal error: too many input files".   
```r  
> cpp -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jambase.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp execunix.cpp fileunix.cpp pathunix.cpp -o b2  
cpp: fatal error: too many input files  
compilation terminated.  
```  
Can you please help me to figure out what's wrong?   
[build.log](https://github.com/boostorg/boost/files/4713381/build.log)

---

## Comment 1

> Username: bergen288  
> Created at: 2020-06-15 20:44:56 UTC  
> Url: https://github.com/boostorg/build/issues/613#issuecomment-644379421  

duplicate.

---

## Comment 2

> Username: bemehiser  
> Created at: 2021-03-11 04:07:19 UTC  
> Url: https://github.com/boostorg/build/issues/613#issuecomment-796431241  

Duplicate of https://github.com/boostorg/build/issues/614

---

## Comment 3

> Username: PositiveTom  
> Created at: 2022-09-12 13:11:53 UTC  
> Url: https://github.com/boostorg/build/issues/613#issuecomment-1243720490  

i also ran into this problem. how do you solve this problem now?

---

## Comment 4

> Username: janwilmans  
> Created at: 2023-06-15 13:03:39 UTC  
> Url: https://github.com/boostorg/build/issues/613#issuecomment-1593005890  

workaround: https://github.com/bfgroup/b2/issues/323#issuecomment-1592899069
