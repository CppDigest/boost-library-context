# #616 - Boost issue compiling on Red Hat 8 with system gcc/8.3.1 [Closed]

> Username: justbennet  
> Created at: 2022-02-01 16:46:00 UTC  
> Updated at: 2022-02-05 15:35:13 UTC  
> Closed at: 2022-02-05 15:35:13 UTC  
> Url: https://github.com/boostorg/boost/issues/616  

We recently tried to install Boost 1_78_0 on our Red Hat 8.3 system, which has GCC 8.3.1.  Red Hat has 'hardened' their installation of GCC such that the precompiled headers generate errors.  We also tried this on an system that was updated to Red Hat 8.5, which provides GCC 8.5.0 with comparable results.  
  
Is there a configuration flag we can give to disable use of precompiled headers?  
  
I am using  
  
```  
$ ./bootstrap.sh --prefix=/tmp/local  
$ ./b2 --prefix=/tmp/bennetsw/local  
```  
  
and I get errors that look like these.  
  
```  
gcc.compile.c++ bin.v2/libs/math/build/gcc-8/release/threading-multi/visibility-hidden/acosh.o  
cc1plus: warning: ./bin.v2/libs/math/build/gcc-8/release/threading-multi/visibility-hidden/pch.gch: had text segment at different address  
cc1plus: error: one or more PCH files were found, but they were invalid  
cc1plus: fatal error: bin.v2/libs/math/build/gcc-8/release/threading-multi/visibility-hidden/pch: No such file or directory  
compilation terminated.  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fvisibility=hidden -Winvalid-pch -DBOOST_ALL_NO_LIB=1 -DBOOST_BUILD_PCH_ENABLED -DBOOST_MATH_TR1_DYN_LINK=1 -DNDEBUG -include"bin.v2/libs/math/build/gcc-8/release/threading-multi/visibility-hidden/pch" -I"." -I"libs/math/src/tr1"  -c -o "bin.v2/libs/math/build/gcc-8/release/threading-multi/visibility-hidden/acosh.o" "libs/math/build/../src/tr1/acosh.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/math/build/gcc-8/release/threading-multi/visibility-hidden/acosh.o...  
```  
  
I did not see anything in the help for either `bootstrap.sh` or for `b2` that looks like it would address this.  Any help would be appreciated.

---

## Comment 1

> Username: justbennet  
> Created at: 2022-02-05 15:35:13 UTC  
> Url: https://github.com/boostorg/boost/issues/616#issuecomment-1030645773  

A colleague found the option to suppress use of precompiled headers.  For anyone else who might be looking, this seems to work.  After running the usual `bootstrap.sh` command to configure `b2`, use  
  
```  
./b2 --prefix=${PREFIX} --build-dir=${BUILD_DIR} pch=off ${CONFIG_OPTIONS}  
./b2 --prefix=${PREFIX} --build-dir=${BUILD_DIR} pch=off ${CONFIG_OPTIONS} install  
```  
  
where `${CONFIG_OPTIONS}` are any other options passed to `b2`.  
  
It may be obvious to most people, but it was not obvious to us that the `pch=off` option needs to be on both the build and install.
