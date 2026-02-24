# #614 - boost compilation failed on AIX7.2 (gcc unsupported or too many input files) [Closed]

> Username: bergen288  
> Created at: 2020-06-03 17:48:22 UTC  
> Updated at: 2024-05-09 11:56:15 UTC  
> Closed at: 2024-05-09 11:56:15 UTC  
> Url: https://github.com/boostorg/build/issues/614  

I believe I had submitted this issue here a couple of days ago.  Somehow, I can't find it anymore.  Let me try one more time.  
I am trying to compile boost_1_73_0 on my AIX server. The default compiler is gcc8.3.0. However, bootstrap.sh says gcc doesn't support C++11 while it also gives list of toolsets including gcc.   See attached bootstrap.log file.  
[bootstrap.log](https://github.com/boostorg/boost/files/4725167/bootstrap.log)  
  
If I run build.sh inside tools/build/src/engine, it won't complain about gcc, but it gives me "cpp: fatal error: too many input files" as shown below. Can you please help me to figure out what's wrong?   
```r  
cpp -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jambase.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp execunix.cpp fileunix.cpp pathunix.cpp -o b2  
cpp: fatal error: too many input files  
compilation terminated.  
```  
[build.log](https://github.com/boostorg/boost/files/4725180/build.log)

---

## Comment 1

> Username: bergen288  
> Created at: 2020-06-12 21:26:24 UTC  
> Url: https://github.com/boostorg/build/issues/614#issuecomment-643489929  

There are 4 major obstacles to compile boost on AIX7.2 server:  
  
1. Not sure why, hard code ```gcc``` as below at line 499 in boost_1_73_0/tools/build/src/engine/build.sh will resolve "too many input files".  
```r  
echo_run gcc ${CXXFLAGS} ${B2_CXXFLAGS} ${B2_SOURCES} -o b2  
```  
2. the thread of ```std::thread::hardware_concurrency``` in boost_1_73_0/tools/build/src/engine/sysinfo.cpp is undefined.    
```r  
    unsigned int std_thread_hardware_concurrency()  
    {  
        return std::thread::hardware_concurrency();   
    }  
```  
I copied following from line 489 in boost_1_73_0/libs/thread/src/pthread/thread.cpp to replace the **return line**:  
```r      
#if defined(PTW32_VERSION) || defined(__hpux)  
        return pthread_num_processors_np();  
#elif defined(__APPLE__) || defined(__FreeBSD__)  
        int count;  
        size_t size=sizeof(count);  
        return sysctlbyname("hw.ncpu",&count,&size,NULL,0)?0:count;  
#elif defined(BOOST_HAS_UNISTD_H) && defined(_SC_NPROCESSORS_ONLN)  
        int const count=sysconf(_SC_NPROCESSORS_ONLN);  
        return (count>0)?count:0;  
#elif defined(__VXWORKS__)  
        cpuset_t set =  ::vxCpuEnabledGet();  
  #ifdef __DCC__  
        int i;  
        for( i = 0; set; ++i)  
        {  
           set &= set -1;  
        }  
        return(i);  
  #else  
        return (__builtin_popcount(set) );  
  #endif  
#elif defined(__GLIBC__)  
        return get_nprocs();  
#else  
        return 0;  
#endif  
```  
3. There is ```undefined reference to `__gxx_personality_v0'```.  Define ```fno-exceptions``` as below in build.sh to disable it.  
```r  
    CXXFLAGS="${CXXFLAGS} -fno-exceptions"   
```  
  
4. Of course, you have to run ```boost_1_73_0/tools/build/src/engine/build.sh```  instead of  ```boost_1_73_0/bootstrap.sh```.  
  
It's very hard work to compile boost on AIX.  I hope someone else may find my experience helpful.

---

## Comment 2

> Username: doug-lew-diagraph  
> Created at: 2021-01-06 21:33:47 UTC  
> Url: https://github.com/boostorg/build/issues/614#issuecomment-755728103  

I was running into a similar issue on MinGW, too many input files.  I made the mod to build.sh and that solved that issue for me.  Thank you for the update!

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:30 UTC  
> Url: https://github.com/boostorg/build/issues/614#issuecomment-850859613  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 4

> Username: edubergeek  
> Created at: 2022-02-26 22:11:01 UTC  
> Updated at: 2022-02-26 22:14:48 UTC  
> Url: https://github.com/boostorg/build/issues/614#issuecomment-1052692582  

Thanks @bergen288   
I had the exact same problems on CentOS Linux release `7.9.2009` with `boost_1_77_0`  
For problem #3: `undefined reference to '__gxx_personality_v0'`  
I was able to continue using ./bootstrap.sh by adding `-lstdc++` before `-o b2` on the same line where I hardcoded `gcc`.  
  
`    ( B2_VERBOSE_OPT=${TRUE} echo_run gcc ${CXXFLAGS}  ${B2_CXXFLAGS} ${B2_SOURCES} -lstdc++ -o b2 )`

---

## Comment 5

> Username: yihuajack  
> Created at: 2022-08-19 16:22:13 UTC  
> Url: https://github.com/boostorg/build/issues/614#issuecomment-1220862240  

Same issue on CentOS release 6.10 (Final) with GCC 4.4.7 20120313 (Red Hat 4.4.7-23)

---

## Comment 6

> Username: janwilmans  
> Created at: 2023-06-15 11:12:34 UTC  
> Updated at: 2023-06-15 11:13:08 UTC  
> Url: https://github.com/boostorg/build/issues/614#issuecomment-1592848192  

Build ether 1.78 or 1.82, I have the same problem:  
This is on a Debian-10 (buster) docker image.  
  
```  
jan.wilmans@HAMILTON246{~/home/project/packages-repo/boost/boost/boost_1_78_0}: ./bootstrap.sh  
Building B2 engine..  
  
###  
###  
### Using 'cxx' toolset.  
###  
###  
  
cpp (Debian 8.3.0-6) 8.3.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
  
###  
###  
  
> cpp -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp execunix.cpp filesys.cpp filent.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam_strings.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp -o b2  
cpp: fatal error: too many input files  
compilation terminated.  
> cp b2 bjam  
cp: cannot stat 'b2': No such file or directory  
  
Failed to build B2 build engine  
  
```

---

## Comment 7

> Username: janwilmans  
> Created at: 2023-06-15 13:04:29 UTC  
> Url: https://github.com/boostorg/build/issues/614#issuecomment-1593007230  

workaround: https://github.com/bfgroup/b2/issues/323#issuecomment-1592899069
