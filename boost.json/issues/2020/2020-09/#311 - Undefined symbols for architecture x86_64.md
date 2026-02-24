# #311 - Undefined symbols for architecture x86_64 [Closed]

> Username: saketpadhi  
> Created at: 2020-09-11 01:27:41 UTC  
> Updated at: 2020-09-12 02:49:30 UTC  
> Closed at: 2020-09-12 02:49:30 UTC  
> Url: https://github.com/boostorg/json/issues/311  

On MacOS Catalina 10.15.6 running :  
`g++-10 -std=c++17 -I /Users/saket/Documents/cifra/boost_1_74_0 -I /Users/saket/Documents/cifra/json-master/inc  
lude cifra.cpp config.cpp -o cifra`  
  
produces an error :   
`Undefined symbols for architecture x86_64:`  
` "__ZN5boost9container3pmr20get_default_resourceEv", referenced from:`  
`     __ZN5boost9container3pmr21polymorphic_allocatorINS_4json5valueEEC1EPNS1_15memory_resourceE in ccxiC5lx.o`  
`ld: symbol(s) not found for architecture x86_64`  
`collect2: error: ld returned 1 exit status`  
  
I have used `#include <boost/json/src.hpp>` to include the library. I am using it with boost 1.74 in header only mode.  
  
This is the code that is producing the error :   
`json::object obj;`  
`obj[ "plaintext" ] = {};`  
`json::value jv(obj);`  
`std::string serialized_json_object = json::serialize(jv);`  
  
Has anybody experienced this ? I googled around but couldn't find much. Please let me know if this is the wrong place to ask. Thanks !

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-11 02:42:13 UTC  
> Url: https://github.com/boostorg/json/issues/311#issuecomment-690839229  

> Has anybody experienced this ? I googled around but couldn't find much. Please let me know if this is the wrong place to ask. Thanks !  
  
Yes, I've experienced it :) You need a version of Boost.Container which has this commit: https://github.com/boostorg/container/commit/0b297019ec43483f523a3270b632fecbc3ce5e63  
  
No currently released Boost version has this. For now, you will need to link to Boost.Container. If Boost.JSON is shipped with Boost, then it will have the fix for Boost.Container. An alternative is to define `BOOST_JSON_STANDALONE`, which requires C++17 (see docs).

---

## Comment 2

> Username: saketpadhi  
> Created at: 2020-09-12 02:39:47 UTC  
> Url: https://github.com/boostorg/json/issues/311#issuecomment-691388177  

Thank you for your help :)  
Linking to the Boost.Container with those fixes you mentioned worked.
