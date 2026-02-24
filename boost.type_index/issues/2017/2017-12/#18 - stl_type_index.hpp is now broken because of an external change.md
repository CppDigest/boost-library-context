# #18 - stl_type_index.hpp is now broken because of an external change [Closed]

> Username: jeking3  
> Created at: 2017-12-28 20:22:04 UTC  
> Updated at: 2018-01-14 21:01:35 UTC  
> Closed at: 2018-01-14 21:01:35 UTC  
> Url: https://github.com/boostorg/type_index/issues/18  

Cause:  
  
https://github.com/boostorg/functional/commit/16a4ab6de637ba4bd6b8a592a5d13623341d015c  
  
While building Boost.DateTime, which includes Boost.Serialization, which includes Boost.TypeIndex:  
  
```  
In file included from ./boost/type_index.hpp:29:0,  
                 from ./boost/function/function_base.hpp:21,  
                 from ./boost/function/detail/prologue.hpp:17,  
                 from ./boost/function.hpp:30,  
                 from libs/serialization/src/basic_xml_grammar.ipp:37,  
                 from libs/serialization/src/xml_grammar.cpp:64:  
./boost/type_index/stl_type_index.hpp:46:40: fatal error: boost/functional/hash.hpp: No such file or directory  
 #   include <boost/functional/hash.hpp>  
                                        ^  
compilation terminated.  
    "g++"   -O0 -fno-inline -Wall -g -fPIC -m64 -std=c++03 -ftemplate-depth-255 -fvisibility=hidden -fvisibility-inlines-hidden -DBOOST_ALL_NO_LIB=1 -DBOOST_SERIALIZATION_DYN_LINK=1  -I"." -c -o "bin.v2/libs/serialization/build/gcc-gnu-4.8/debug/cxxstd-03-iso/threadapi-pthread/xml_grammar.o" "libs/serialization/src/xml_grammar.cpp"  
```  
  
Either fix it here, or @danieljames needs to add a forwarding header in Boost.Functional for compatibility.

---

## Comment 1

> Username: danieljames  
> Created at: 2017-12-28 21:07:50 UTC  
> Url: https://github.com/boostorg/type_index/issues/18#issuecomment-354358089  

It should be in the `container_hash` module.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-01-14 21:01:35 UTC  
> Url: https://github.com/boostorg/type_index/issues/18#issuecomment-357542129  

This was fixed in  82ed016
