# #24 - unary_function has been removed from libc++ [Closed]

> Username: davidstone  
> Created at: 2022-07-10 15:16:13 UTC  
> Updated at: 2022-07-11 18:44:04 UTC  
> Closed at: 2022-07-11 18:44:04 UTC  
> Url: https://github.com/boostorg/container_hash/issues/24  

https://github.com/boostorg/container_hash/issues/22 talks about a similar issue with gcc warning about a deprecated use. Trunk libc++ has removed this function for the 15.0 release in C++17 and newer, so now I am getting an error:  
  
```  
/usr/include/boost/container_hash/hash.hpp:132:33: error: no template named 'unary_function' in namespace 'std'; did you mean '__unary_function'?  
        struct hash_base : std::unary_function<T, std::size_t> {};  
                           ~~~~~^  
/home/david/llvm/build/bin/../include/c++/v1/__functional/unary_function.h:46:1: note: '__unary_function' declared here  
using __unary_function = __unary_function_keep_layout_base<_Arg, _Result>;  
^  
```  
  
See https://github.com/llvm/llvm-project/blob/main/libcxx/docs/ReleaseNotes.rst

---

## Comment 1

> Username: pdimov  
> Created at: 2022-07-10 15:20:38 UTC  
> Url: https://github.com/boostorg/container_hash/issues/24#issuecomment-1179747741  

Sounds like the Config change needs to be applied for libc++ as well. (cc @jzmaddock)
