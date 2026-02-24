# #61 - Fails to build with `toolset=msvc-14.0 cxxstd=latest` [Closed]

> Username: pdimov  
> Created at: 2022-06-03 18:09:45 UTC  
> Updated at: 2022-06-04 17:34:19 UTC  
> Closed at: 2022-06-04 17:22:15 UTC  
> Url: https://github.com/boostorg/atomic/issues/61  

With the following error:  
```  
compile-c-c++ ..\..\bin.v2\libs\atomic\build\msvc-14.0\debug\cxxstd-latest-iso\threading-multi\lock_pool.obj  
lock_pool.cpp  
C:\boost-git\develop\boost/atomic/detail/type_traits/has_unique_object_representations.hpp(33): error C2039: 'has_unique_object_representations': is not a member of 'std'  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\limits(18): note: see declaration of 'std'  
C:\boost-git\develop\boost/atomic/detail/type_traits/has_unique_object_representations.hpp(33): error C2873: 'has_unique_object_representations': symbol cannot be used in a using-declaration  
```  
and  
```  
compile-c-c++ ..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\cxxstd-latest-iso\threading-multi\path.obj  
path.cpp  
C:\boost-git\develop\boost/atomic/detail/type_traits/has_unique_object_representations.hpp(33): error C2039: 'has_unique_object_representations': is not a member of 'std'  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\algorithm(14): note: see declaration of 'std'  
C:\boost-git\develop\boost/atomic/detail/type_traits/has_unique_object_representations.hpp(33): error C2873: 'has_unique_object_representations': symbol cannot be used in a using-declaration  
```  
and so on.  
  
I was surprised that this trait went into C++17, I don't remember as part of what changes. But in any case, VS 2015 doesn't seem to have it.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-06-04 17:34:19 UTC  
> Url: https://github.com/boostorg/atomic/issues/61#issuecomment-1146656298  

Thanks for the report.
