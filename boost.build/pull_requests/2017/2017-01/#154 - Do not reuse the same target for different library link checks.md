# #154 Do not reuse the same target for different library link checks [Merged]

> Username: fwyzard  
> Created at: 2017-01-14 11:16:59 UTC  
> Updated at: 2021-10-02 22:18:58 UTC  
> Merged at: 2017-01-17 02:07:31 UTC  
> Closed at: 2017-01-17 02:07:31 UTC  
> Url: https://github.com/boostorg/build/pull/154  

Using a global .main.cpp target causes conflicts when testing for libraries in different directories.  
For example, trying to build boost.iostreams with  
```  
$(BUILD)/b2 -q \  
   -d$(DEBUG) \  
   -sBZIP2_INCLUDE=../bzip2-1.0.6 \  
   -sBZIP2_LIBPATH=../bzip2-1.0.6 \  
   -sZLIB_INCLUDE=../zlib-1.2.10 \  
   -sZLIB_LIBPATH=../zlib-1.2.10 \  
   --build-dir=build \  
   --stage-dir=stage \  
   toolset=gcc \  
   link=shared \  
   threading=multi \  
   variant=release \  
   cxxflags="-std=c++11 -O2" \  
   stage  
```  
results in  
```  
error: at /home/fwyzard/src/boost/boost_1_63_0/tools/build/src/kernel/modules.jam:107  
error: Name clash for '<pbuild/boost/bin.v2/standalone/ac/gcc-5.4.1/release/threading-multi>main.o'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <dll-path>../zlib-1.2.10 <library-path>../zlib-1.2.10 <xdll-path>../zlib-1.2.10  
error:     -  <dll-path>../bzip2-1.0.6 <library-path>../bzip2-1.0.6 <xdll-path>../bzip2-1.0.6  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
Using a different target for each check solves the issue.

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-01-15 13:50:20 UTC  
> Url: https://github.com/boostorg/build/pull/154#issuecomment-272696427  

This looks good to me on my tests. Does anyone object if I merge this pull request ?

---

## Comment 2

> Username: swatanabe  
> Created_at: 2017-01-17 00:42:55 UTC  
> Url: https://github.com/boostorg/build/pull/154#issuecomment-272993545  

AMDG  
  
On 01/15/2017 06:50 AM, Edward Diener wrote:  
> This looks good to me on my tests. Does anyone object if I merge this pull request ?  
>   
  
Go ahead.  
  
In Christ,  
Steven Watanabe

---
