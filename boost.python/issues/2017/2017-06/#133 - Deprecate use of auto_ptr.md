# #133 - Deprecate use of auto_ptr [Closed]

> Username: shreyans800755  
> Created at: 2017-06-08 19:05:29 UTC  
> Updated at: 2017-06-08 19:29:42 UTC  
> Closed at: 2017-06-08 19:29:05 UTC  
> Url: https://github.com/boostorg/python/issues/133  

Currently, on compiling boostorg/python with `gcc version 6.3.0 20170519 (Ubuntu/Linaro 6.3.0-18ubuntu2~14.04)` is giving a lot of warnings. These warnings are related to `auto_ptr`.  
Here's a sample warning:  
  
> Compiling bin.SCons/gcc-6.3.0/release/dynamic/threading-multi/test/vector_indexing_suite.os  
In file included from include/boost/python/detail/is_xxx.hpp:8:0,  
                 from include/boost/python/detail/is_auto_ptr.hpp:9,  
                 from include/boost/python/detail/copy_ctor_mutates_rhs.hpp:8,  
                 from include/boost/python/detail/value_arg.hpp:7,  
                 from include/boost/python/object/forward.hpp:13,  
                 from include/boost/python/object/pointer_holder.hpp:16,  
                 from include/boost/python/to_python_indirect.hpp:10,  
                 from include/boost/python/converter/arg_to_python.hpp:10,  
                 from include/boost/python/call.hpp:15,  
                 from include/boost/python/object_core.hpp:14,  
                 from include/boost/python/object/class.hpp:9,  
                 from include/boost/python/class.hpp:13,  
                 from include/boost/python/suite/indexing/indexing_suite.hpp:9,  
                 from include/boost/python/suite/indexing/vector_indexing_suite.hpp:9,  
                 from bin.SCons/gcc-6.3.0/release/dynamic/threading-multi/test/vector_indexing_suite.cpp:5:  
include/boost/python/detail/is_auto_ptr.hpp:17:40: warning: ‘template<class> class std::auto_ptr’ is deprecated [-Wdeprecated-declarations]  
 BOOST_PYTHON_IS_XXX_DEF(auto_ptr, std::auto_ptr, 1)  
                                        ^  
include/boost/python/detail/is_auto_ptr.hpp:17:1: note: in expansion of macro ‘BOOST_PYTHON_IS_XXX_DEF’  
 BOOST_PYTHON_IS_XXX_DEF(auto_ptr, std::auto_ptr, 1)  
 ^~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/c++/6/memory:81:0,  
                 from /usr/include/boost/config/no_tr1/memory.hpp:21,  
                 from /usr/include/boost/get_pointer.hpp:14,  
                 from include/boost/python/object/pointer_holder.hpp:11,  
                 from include/boost/python/to_python_indirect.hpp:10,  
                 from include/boost/python/converter/arg_to_python.hpp:10,  
                 from include/boost/python/call.hpp:15,  
                 from include/boost/python/object_core.hpp:14,  
                 from include/boost/python/object/class.hpp:9,  
                 from include/boost/python/class.hpp:13,  
                 from include/boost/python/suite/indexing/indexing_suite.hpp:9,  
                 from include/boost/python/suite/indexing/vector_indexing_suite.hpp:9,  
                 from bin.SCons/gcc-6.3.0/release/dynamic/threading-multi/test/vector_indexing_suite.cpp:5:  
/usr/include/c++/6/bits/unique_ptr.h:49:28: note: declared here  
   template<typename> class auto_ptr;  
                            ^~~~~~~~  
Linking bin.SCons/gcc-6.3.0/release/dynamic/threading-multi/test/vector_indexing_suite_ext.so  
Testing bin.SCons/gcc-6.3.0/release/dynamic/threading-multi/test/vector_indexing_suite.py  
PASS

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-06-08 19:20:57 UTC  
> Url: https://github.com/boostorg/python/issues/133#issuecomment-307201754  

Yes. I don't think `std::auto_ptr` is being used right now anywhere in Boost.Python (this was fixed in https://github.com/boostorg/python/issues/80). However, we still need to support user-code using it, and thus need `boost/python/detail/is_auto_ptr.hpp`, which causes the above warning.

---

## Comment 2

> Username: shreyans800755  
> Created at: 2017-06-08 19:21:33 UTC  
> Updated at: 2017-06-08 19:25:00 UTC  
> Url: https://github.com/boostorg/python/issues/133#issuecomment-307201913  

This warning messages are being generated for most of the test cases. This is also spamming the build and test logs a lot.  
May we can think of disabling the messages until it is fixed, so that, development is not slowed down due to this.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-06-08 19:26:04 UTC  
> Updated at: 2017-06-08 19:29:42 UTC  
> Url: https://github.com/boostorg/python/issues/133#issuecomment-307203017  

Just use `-Wno-deprecated-declarations` to silence the compiler.  
(I happen to be working on a new build tool, using Boost.Python as the guinea pig. Look what I'm using here: https://github.com/stefanseefeld/boost.python/blob/faber/fabscript#L83-L84 :-) )

---

## Comment 4

> Username: shreyans800755  
> Created at: 2017-06-08 19:29:05 UTC  
> Url: https://github.com/boostorg/python/issues/133#issuecomment-307203773  

Duplicate of #80 .
