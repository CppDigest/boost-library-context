# #792 - Compilation errors with boost::compute 1.67.0 at VS 2017 [Closed]

> Username: XNQrE  
> Created at: 2018-08-08 13:33:46 UTC  
> Updated at: 2018-08-10 12:46:33 UTC  
> Closed at: 2018-08-10 12:46:11 UTC  
> Url: https://github.com/boostorg/compute/issues/792  

I haven't reproduced more specific issues to example code (they are various in relation to adding additional compute headers after using system or vector hpp), but it's telling that if the catch-all  boost/compute.hpp is included to an empty main function, there's a compilation failure. It does go away if individual headers are used carefully but that shouldn't be considered normal.   
  
```  
#include <boost/compute.hpp>   
  
int main()  
{  
}  
```  
  
> 1>Source.cpp  
> 1>c:\boost_1_67_0\boost\compute\algorithm\detail\reduce_by_key_with_scan.hpp(96): error C2676: binary '+': 'boost::compute::buffer_iterator<T>' does not define this operator or a conversion to a type acceptable to the predefined operator  
> 1>        with  
> 1>        [  
> 1>            T=cl_uint  
> 1>        ]  
> 1>Done building project "example.vcxproj" -- FAILED.  
  
PS. On gcc 8.2.0 it's fine

---

## Comment 1

> Username: XNQrE  
> Created at: 2018-08-08 13:55:36 UTC  
> Updated at: 2018-08-08 13:56:51 UTC  
> Url: https://github.com/boostorg/compute/issues/792#issuecomment-411414178  

1.68.0.b1 has additional errors  
  
> 1>c:\_alt\2\boost\range\concepts.hpp(257): error C3646: 'n': unknown override specifier  
> 1>c:\_alt\2\boost\range\concepts.hpp(264): note: see reference to class template instantiation 'boost::range_detail::RandomAccessIteratorConcept<Iterator>' being compiled  
> 1>c:\_alt\2\boost\range\concepts.hpp(257): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
> 1>c:\_alt\2\boost\compute\algorithm\detail\reduce_by_key_with_scan.hpp(96): error C2676: binary '+': 'boost::compute::buffer_iterator<T>' does not define this operator or a conversion to a type acceptable to the predefined operator  
> 1>        with  
> 1>        [  
> 1>            T=cl_uint  
> 1>        ]  
> 1>Done building project "example.vcxproj" -- FAILED.

---

## Comment 2

> Username: XNQrE  
> Created at: 2018-08-10 12:46:11 UTC  
> Updated at: 2018-08-10 12:46:33 UTC  
> Url: https://github.com/boostorg/compute/issues/792#issuecomment-412071721  

I'm closing it since all issues appear to be strictly related to VS at the current stable version and not universal. Emphasis on "appear" since I can't be certain,
