# #167 Fix missing-field-initializers error in GCC 4.8 and 4.9 [Merged]

> Username: k3DW  
> Created at: 2024-08-22 18:29:30 UTC  
> Updated at: 2024-08-22 23:42:47 UTC  
> Merged at: 2024-08-22 22:43:29 UTC  
> Closed at: 2024-08-22 22:43:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/167  

Failure seen in [unordered#274](https://github.com/boostorg/unordered/pull/274)  
[CE link](https://godbolt.org/z/dcYes3PTc)

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-08-22 18:35:35 UTC  
> Url: https://github.com/boostorg/uuid/pull/167#issuecomment-2305399559  

The correct fix is `{{}}`, but why does this warning not appear in the UUID tests? They also use -pedantic.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-08-22 18:38:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/167#issuecomment-2305403874  

Oh I see, I'm not using -Werror.  
  
```  
gcc.compile.c++ bin.v2/libs/uuid/test/~hdr-decl-uuid_serialize~hpp.test/gcc-4.8/debug/x86_32/cxxstd-11-iso/threading-multi/visibility-hidden/~hdr-decl-uuid_serialize~hpp.o  
In file included from ./boost/uuid/uuid_serialize.hpp:11:0,  
                 from libs/uuid/test/compile/decl_header.cpp:19:  
./boost/uuid/uuid.hpp: In member function 'boost::uuids::uuid::node_type boost::uuids::uuid::node_identifier() const':  
./boost/uuid/uuid.hpp:269:27: warning: missing initializer for member 'std::array<unsigned char, 6u>::_M_elems' [-Wmissing-field-initializers]  
         node_type node = {};  
                           ^  
```

---

## Comment 3

> Username: k3DW  
> Created_at: 2024-08-22 18:38:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/167#issuecomment-2305404722  

> The correct fix is `{{}}`, but why does this warning not appear in the UUID tests? They also use -pedantic.  
  
Corrected, thanks. I'm not sure why this isn't caught in other places. I'm also not sure why this was only caught in my current Unordered PR, and not in any of the other Unordered tests also using this file.

---
