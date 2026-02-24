# #162 - memcpy warning [Closed]

> Username: alandefreitas  
> Created at: 2022-04-01 20:47:50 UTC  
> Updated at: 2022-07-31 03:29:35 UTC  
> Closed at: 2022-07-31 03:29:35 UTC  
> Url: https://github.com/boostorg/url/issues/162  

The call to `memcpy` in   
  
https://github.com/CPPAlliance/url/blob/1a9b2f9faa8fa212c8088fcf15e96d0938aab5aa/include/boost/url/impl/const_string.hpp#L188  
  
leads to this warning  
  
```  
warning: ‘void* __builtin_memcpy(void*, const void*, long unsigned int)’ forming offset [15, 32] is out of the bounds [0, 15] of object ‘buf’ with type ‘char [15]’ [-Warray-bounds]  
```  
  
The complete warning:  
  
```  
In function ‘void* memcpy(void*, const void*, size_t)’,  
    inlined from ‘boost::urls::const_string::const_string(boost::urls::string_view, const Allocator&) [with Allocator = std::allocator<char>]’ at ./boost/url/impl/const_string.hpp:188:16,  
    inlined from ‘boost::urls::const_string boost::urls::ipv4_address::to_string(const Allocator&) const [with Allocator = std::allocator<char>]’ at ./boost/url/ipv4_address.hpp:175:35,  
    inlined from ‘void boost::urls::ipv4_address_test::testMembers()’ at libs/url/test/unit/ipv4_address.cpp:99:13:  
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:34:33: warning: ‘void* __builtin_memcpy(void*, const void*, long unsigned int)’ forming offset [15, 32] is out of the bounds [0, 15] of object ‘buf’ with type ‘char [15]’ [-Warray-bounds]  
   34 |   return __builtin___memcpy_chk (__dest, __src, __len, __bos0 (__dest));  
      |          ~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from libs/url/test/unit/ipv4_address.cpp:11:  
./boost/url/ipv4_address.hpp: In function ‘void boost::urls::ipv4_address_test::testMembers()’:  
./boost/url/ipv4_address.hpp:172:14: note: ‘buf’ declared here  
  172 |         char buf[max_str_len];  
      |              ^~~  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-31 03:29:35 UTC  
> Url: https://github.com/boostorg/url/issues/162#issuecomment-1200340469  

obsolete
