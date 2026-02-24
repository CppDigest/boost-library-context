# #627 - Fails to find a static library with runtime-link=shared [Open]

> Username: hamstergene  
> Created at: 2022-02-22 00:53:09 UTC  
> Updated at: 2022-02-22 02:13:45 UTC  
> Url: https://github.com/boostorg/boost/issues/627  

B2 fails to find static library (zlib) when I change `runtime-link=static` to `runtime-link=shared`:  
  
```  
$ ls /path/to/include/x64-debug  
zconf.h  zlib.h  
  
$ ls /path/to/lib/x64-debug  
libz.a  
  
$ ./b2 -n --reconfigure --debug-configure --with-iostreams -s ZLIB_INCLUDE=/path/to/include/x64-debug -s ZLIB_LIBPATH=/path/to/lib/x64-debug -s ZLIB_NAME=libz link=static runtime-link=static  
  
notice: [zlib] Using pre-installed library  
notice: [zlib] Condition  
  
Building the Boost C++ Libraries.  
    - zlib                     : yes [3]  
  
$ ./b2 -n --reconfigure --debug-configure --with-iostreams -s ZLIB_INCLUDE=/path/to/include/x64-debug -s ZLIB_LIBPATH=/path/to/lib/x64-debug -s ZLIB_NAME=libz link=static runtime-link=shared  
  
notice: [zlib] Using pre-installed library  
notice: [zlib] Condition  
  
Building the Boost C++ Libraries.  
    - zlib                     : no [3]  
  
[3] link=static  
```  
  
No other difference in command line nor in environment, other than `runtime-link=static` vs. `runtime-link=shared`.  
  
Tried current tip of the master branch (2b06d9f20560a4f7292833fcb71d8c6a3652ca76) as well as an old version 1.71.0.  
  
I expect C++ runtime type selection to not affect library search.

---

## Comment 1

> Username: hamstergene  
> Created at: 2022-02-22 01:01:15 UTC  
> Url: https://github.com/boostorg/boost/issues/627#issuecomment-1047330600  

Linux, GCC v7  
  
When zlib is not found (`runtime-link=shared`), `bin.v2/config.log` contains  
  
```  
set "ac-library-zlib-<address-model>64-<architecture>x86-<link>static-<target-os>linux-<toolset-gcc:version>7-<toolset>gcc" : "missing" ;  
```  
  
If found (`runtime-link=static`), then `bin.v2/config.log` says  
  
```  
set "ac-library-zlib-<address-model>64-<architecture>x86-<link>static-<target-os>linux-<toolset-gcc:version>7-<toolset>gcc" : "/path/to/lib/x64-debug" "z" "<link>shared" ;  
```  
  
Tried `--debug-generator` but there are no other mentions of `zlib` in the log.

---

## Comment 2

> Username: hamstergene  
> Created at: 2022-02-22 02:13:45 UTC  
> Url: https://github.com/boostorg/boost/issues/627#issuecomment-1047360446  

Looks like this is caused by (incorrectly) passing `ZLIB_NAME=libz` instead of `ZLIB_NAME=z`, but I don't any reason why one would falsely succeed and another not.
