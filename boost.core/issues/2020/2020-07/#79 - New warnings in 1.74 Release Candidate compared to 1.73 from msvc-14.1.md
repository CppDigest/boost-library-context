# #79 - New warnings in 1.74 Release Candidate compared to 1.73 from msvc-14.1 [Closed]

> Username: thebrandre  
> Created at: 2020-07-17 12:27:42 UTC  
> Updated at: 2020-07-18 21:53:21 UTC  
> Closed at: 2020-07-18 21:53:21 UTC  
> Url: https://github.com/boostorg/core/issues/79  

We get a lot of noisy warnings from msvc-14.1 when including `circular_buffer.hpp` with the 1.74 Release Candidate. These warnings do not appear with the current stable version of boost 1.73.  
This issue seems related to #75 but, as far as I can see, the release candidate should already have this fix?  
  
Simple example:  
  
```cpp  
#include <boost/circular_buffer.hpp>  
  
int main() {  
	boost::circular_buffer<int> Buffer(5);  
	Buffer.push_back(0);  
	return Buffer[0];  
}  
```  
Compiling it with VS2017's compiler Microsoft (R) C/C++ Optimizing Compiler Version 19.16.27042 for x64 and the stable version of boost produces no warnings:  
```bat  
cl.exe /EHsc main.cpp /ID:\lib\boost_1_73_0 /std:c++17 /W4  
```  
And the same thing with the release candidate of 1.74 via  
```bat  
cl.exe /EHsc main.cpp /ID:\lib\boost_1921ec4c /std:c++17 /W4  
```  
will generate these warnings:  
```  
D:\lib\boost_1921ec4c\boost/core/allocator_access.hpp(150): warning C4996: 'std::allocator<int>::difference_type': warning STL4010: Various members of std::allocator are deprecated in C++17. Use std::allocator_traits instead of accessing these members directly. You can define _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\VC\Tools\MSVC\14.16.27023\include\xmemory0(958): note: see declaration of 'std::allocator<int>::difference_type'  
D:\lib\boost_1921ec4c\boost/circular_buffer/base.hpp(120): note: see reference to class template instantiation 'boost::allocator_difference_type<Alloc,void>' being compiled  
        with  
        [  
            Alloc=std::allocator<int>  
        ]  
main.cpp(4): note: see reference to class template instantiation 'boost::circular_buffer<int,std::allocator<int>>' being compiled  
D:\lib\boost_1921ec4c\boost/core/allocator_access.hpp(169): warning C4996: 'std::allocator<int>::size_type': warning STL4010: Various members of std::allocator are deprecated in C++17. Use std::allocator_traits instead of accessing these members directly. You can define _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\VC\Tools\MSVC\14.16.27023\include\xmemory0(957): note: see declaration of 'std::allocator<int>::size_type'  
D:\lib\boost_1921ec4c\boost/circular_buffer/base.hpp(126): note: see reference to class template instantiation 'boost::allocator_size_type<Alloc,void>' being compiled  
        with  
        [  
            Alloc=std::allocator<int>  
        ]  
```  
  
Surprisingly, this seems not to be the case with VS2019's compiler Microsoft (R) C/C++ Optimizing Compiler Version 19.26.28806 for x86.

---

## Comment 1

> Username: glenfe  
> Created at: 2020-07-17 13:32:27 UTC  
> Url: https://github.com/boostorg/core/issues/79#issuecomment-660109236  

Thanks. I had implemented the MSVC workarounds for `pointer` and `const_pointer`, but not for `size_type` and `difference_type`. Fixed in 484487f in develop. Will get this into the 1.74 release.

---

## Comment 2

> Username: glenfe  
> Created at: 2020-07-17 13:45:12 UTC  
> Url: https://github.com/boostorg/core/issues/79#issuecomment-660115507  

> Surprisingly, this seems not to be the case with VS2019's compiler Microsoft (R) C/C++ Optimizing Compiler Version 19.26.28806 for x86.  
  
That's because Microsoft later removed their `_CXX17_DEPRECATE_OLD_ALLOCATOR_MEMBERS ` from those members in their `std::allocator` definition.

---

## Comment 3

> Username: thebrandre  
> Created at: 2020-07-17 16:12:25 UTC  
> Url: https://github.com/boostorg/core/issues/79#issuecomment-660195579  

Thanks a lot for fixing it so fast!
