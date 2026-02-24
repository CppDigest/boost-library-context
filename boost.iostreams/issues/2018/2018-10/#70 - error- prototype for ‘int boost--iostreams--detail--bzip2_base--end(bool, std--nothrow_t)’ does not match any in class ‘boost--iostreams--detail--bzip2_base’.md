# #70 - error: prototype for ‘int boost::iostreams::detail::bzip2_base::end(bool, std::nothrow_t)’ does not match any in class ‘boost::iostreams::detail::bzip2_base’ [Open]

> Username: automatedbugreportingfacility  
> Created at: 2018-10-21 16:27:51 UTC  
> Updated at: 2018-10-22 20:56:17 UTC  
> Url: https://github.com/boostorg/iostreams/issues/70  

Ubuntu 18.04. Compilation log:  
  
```  
username@username:~/iostreams/build$ b2  
warning: No toolsets are configured.  
warning: Configuring default toolset "gcc".  
warning: If the default is wrong, your build may not work correctly.  
warning: Use the "toolset=xxxxx" option to override our guess.  
warning: For more configuration options, please consult  
warning: http://boost.org/boost-build2/doc/html/bbv2/advanced/configuration.html  
Performing configuration checks  
  
    - bzip2                    : yes (cached)  
    - lzma                     : no  (cached)  
    - zstd                     : no  (cached)  
    - zlib                     : yes (cached)  
...found 47 targets...  
...updating 2 targets...  
gcc.compile.c++ bin/gcc-7.3.0/debug/bzip2.o  
../src/bzip2.cpp:114:5: error: prototype for ‘int boost::iostreams::detail::bzip2_base::end(bool, std::nothrow_t)’ does not match any in class ‘boost::iostreams::detail::bzip2_base’  
 int bzip2_base::end(bool compress, std::nothrow_t)  
     ^~~~~~~~~~  
In file included from ../src/bzip2.cpp:19:0:  
/usr/include/boost/iostreams/filter/bzip2.hpp:178:10: error: candidate is: void boost::iostreams::detail::bzip2_base::end(bool)  
     void end(bool compress);  
          ^~~  
../src/bzip2.cpp: In member function ‘void boost::iostreams::detail::bzip2_base::end(bool)’:  
../src/bzip2.cpp:125:35: error: no matching function for call to ‘boost::iostreams::detail::bzip2_base::end(bool&, const std::nothrow_t&)’  
         end(compress, std::nothrow)  
                                   ^  
../src/bzip2.cpp:122:6: note: candidate: void boost::iostreams::detail::bzip2_base::end(bool)  
 void bzip2_base::end(bool compress)  
      ^~~~~~~~~~  
../src/bzip2.cpp:122:6: note:   candidate expects 1 argument, 2 provided  
  
    "g++"   -fPIC -O0 -fno-inline -Wall -g  -DBOOST_IOSTREAMS_DYN_LINK=1 -DBOOST_IOSTREAMS_USE_DEPRECATED   -c -o "bin/gcc-7.3.0/debug/bzip2.o" "../src/bzip2.cpp"  
  
...failed gcc.compile.c++ bin/gcc-7.3.0/debug/bzip2.o...  
...skipped <pbin/gcc-7.3.0/debug>libboost_iostreams.so for lack of <pbin/gcc-7.3.0/debug>bzip2.o...  
...failed updating 1 target...  
...skipped 1 target...  
```

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-10-22 16:17:01 UTC  
> Url: https://github.com/boostorg/iostreams/issues/70#issuecomment-431883803  

AMDG  
  
Don't try to build a boost library outside of  
the super-project.  It's not going to work.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: automatedbugreportingfacility  
> Created at: 2018-10-22 20:36:47 UTC  
> Updated at: 2018-10-22 20:56:17 UTC  
> Url: https://github.com/boostorg/iostreams/issues/70#issuecomment-431975112  

Ungodly instructions at https://www.boost.org/doc/libs/1_68_0/libs/iostreams/doc/installation.html :  
  
To build with Boost.Build, run b2 **from the directory libs/iostreams/build**, or from the Boost root directory.   
  
made me do it. Why is it even provided as a separate project if you can't build it standalone :( Deities frown upon it.
