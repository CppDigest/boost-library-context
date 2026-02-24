# #92 - C++26 errors: omission of ',' before varargs '...' is deprecated [Closed]

> Username: alandefreitas  
> Created at: 2025-05-16 18:35:21 UTC  
> Updated at: 2025-05-18 20:33:20 UTC  
> Closed at: 2025-05-18 20:33:20 UTC  
> Url: https://github.com/boostorg/intrusive/issues/92  

GCC 15 and Clang 20 include C++26 support.  
  
Both return similar errors:  
  
GCC 15:  
  
```  
  In file included from ./boost/intrusive/detail/key_nodeptr_comp.hpp:25,  
                   from ./boost/intrusive/slist.hpp:36,  
                   from ./boost/container/detail/multiallocation_chain.hpp:32,  
                   from ./boost/container/detail/allocator_version_traits.hpp:27,  
                   from ./boost/container/string.hpp:31,  
                   from libs/url/test/unit/snippets.cpp:12:  
  ./boost/intrusive/detail/ebo_functor_holder.hpp:91:49: error: omission of ',' before varargs '...' is deprecated in C++26 [-Werror=deprecated-variadic-comma-omission]  
     91 | struct is_unary_or_binary_function_impl<R (*)(T0...)>  
        |                                                 ^~~  
        |                                                 ,   
  ./boost/intrusive/detail/ebo_functor_holder.hpp:127:53: error: omission of ',' before varargs '...' is deprecated in C++26 [-Werror=deprecated-variadic-comma-omission]  
    127 | struct is_unary_or_binary_function_impl<R (*)(T0, T1...)>  
        |                                                     ^~~  
        |                                                     ,   
  In file included from ./boost/filesystem/path.hpp:30,  
                   from libs/url/test/unit/snippets.cpp:13:  
  ./boost/iterator/iterator_facade.hpp:378:18: error: 'template<class _Tp> struct std::is_trivial' is deprecated: use 'is_trivially_default_constructible && is_trivially_copyable' instead [-Werror=deprecated-declarations]  
    378 |             std::is_trivial< ValueType >,  
        |                  ^~~~~~~~~~  
  In file included from /usr/include/c++/15/bits/move.h:37,  
                   from /usr/include/c++/15/bits/exception_ptr.h:41,  
                   from /usr/include/c++/15/exception:168,  
                   from /usr/include/c++/15/ios:43,  
                   from /usr/include/c++/15/istream:42,  
                   from /usr/include/c++/15/sstream:42,  
                   from libs/url/extra/test_suite.hpp:19,  
                   from libs/url/test/unit/snippets.cpp:10:  
  /usr/include/c++/15/type_traits:898:5: note: declared here  
    898 |     is_trivial  
        |     ^~~~~~~~~~  
  cc1plus: all warnings being treated as errors  
    
```  
  
Clang 20  
  
```  
  In file included from ./boost/container/map.hpp:30:  
  In file included from ./boost/container/detail/tree.hpp:32:  
  In file included from ./boost/container/detail/compare_functors.hpp:22:  
  ./boost/intrusive/detail/ebo_functor_holder.hpp:91:49: error: declaration of a variadic function without a comma before '...' is deprecated [-Werror,-Wdeprecated-missing-comma-variadic-parameter]  
     91 | struct is_unary_or_binary_function_impl<R (*)(T0...)>  
        |                                                 ^  
        |                                                 ,   
  ./boost/intrusive/detail/ebo_functor_holder.hpp:127:53: error: declaration of a variadic function without a comma before '...' is deprecated [-Werror,-Wdeprecated-missing-comma-variadic-parameter]  
    127 | struct is_unary_or_binary_function_impl<R (*)(T0, T1...)>  
        |                                                     ^  
        |                                                     ,   
  2 errors generated.  
    
```
