# #630 - Visual Studio compiler version mismatch for conancenter packages [Open]

> Username: SimonZimmer  
> Created at: 2022-03-03 11:28:08 UTC  
> Updated at: 2022-03-03 12:00:02 UTC  
> Url: https://github.com/boostorg/boost/issues/630  

I've been using the conan package manager to collect precompiled boost from [conancenter](https://conan.io/center/boost?tab=overview).  
Judging from the conan info, version `1.78` should be available for the compiler version `16`.  
From conancenter and the package `boost/1.78.0:37050c911e5419517ec6e0761c16c6e262b3e2a3` :  
```  
[settings]  
    arch=x86_64  
    build_type=Release  
    compiler=Visual Studio  
    compiler.runtime=MD  
    compiler.version=16  
    os=Windows  
```  
However, when downloading the package through conan and executing `dumpbin /rawdata:1` on `libboost_serialization.lib`, the assembly starts with:  
```  
File Type: LIBRARY  
  
RAW DATA #1  
  00000000: 20 20 20 2F 46 41 49 4C 49 46 4D 49 53 4D 41 54     /FAILIFMISMAT  
  00000010: 43 48 3A 22 5F 4D 53 43 5F 56 45 52 3D 31 39 30  CH:"_MSC_VER=190  
  00000020: 30 22 20 2F 46 41 49 4C 49 46 4D 49 53 4D 41 54  0" /FAILIFMISMAT  
```  
which means according to the [Microsoft documentation](https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros?view=msvc-170) `Visual Studio 2015 14.0` so actually compiler version `14` instead of `16`.  
  
As a consequence I cannot use the package because I have a hard requirement for `Visual Studio 2019 16`.  
  
EDIT:  
Judging from the [b2 documentation](https://www.boost.org/doc/libs/1_76_0/tools/build/doc/html/index.html#bbv2.reference.tools.compilers) it doesn't seem that Visual Studio compiler versions above `14.2` aren't supported at all.
