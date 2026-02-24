# #29 - Uninitialized variable warning pointer_plus_bits.hpp:76 [Closed]

> Username: jeking3  
> Created at: 2017-11-13 16:47:54 UTC  
> Updated at: 2018-03-03 00:58:54 UTC  
> Closed at: 2017-12-09 12:03:44 UTC  
> Url: https://github.com/boostorg/intrusive/issues/29  

Brand new clone of master on Ubuntu Trusty with gcc-7.2 installed, this warning appeared in the build:  
  
Command: `./b2 address-model=64 link=shared threading=multi toolset=gcc-7 variant=release -q -j5`  
Output:  
```  
gcc.compile.c++ bin.v2/libs/log/build/gcc-gnu-7/release/threadapi-pthread/threading-multi/attribute_name.o  
In file included from ./boost/intrusive/detail/rbtree_node.hpp:29:0,  
                 from ./boost/intrusive/set_hook.hpp:20,  
                 from ./boost/intrusive/rbtree.hpp:21,  
                 from ./boost/intrusive/set.hpp:20,  
                 from libs/log/src/attribute_name.cpp:25:  
./boost/intrusive/pointer_plus_bits.hpp: In static member function â€˜static boost::log::v2_mt_posix::attribute_name::id_type boost::log::v2_mt_posix::attribute_name::get_id_from_string(const char*)â€™:  
./boost/intrusive/pointer_plus_bits.hpp:76:48: warning: â€˜<anonymous>â€™ may be used uninitialized in this function [-Wmaybe-uninitialized]  
       n = pointer(uintptr_t(p) | (uintptr_t(n) & Mask));  
                                  ~~~~~~~~~~~~~~^~~~~~~  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-12-08 12:07:15 UTC  
> Url: https://github.com/boostorg/intrusive/issues/29#issuecomment-350248260  

The problem is that indeed (n) is uninitialized, but that's the intention, if you have an uninitialized pointer+bit value and you want to set only the pointer type, the flags part will always be uninitialized, but we don't want to initialize it with some value or want to track if it was previously initialized.  
  
In any case, maybe we can disable, only for this class, this GCC warning so that it does not annoy users.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2017-12-09 12:03:44 UTC  
> Url: https://github.com/boostorg/intrusive/issues/29#issuecomment-350455105  

Commit  
  
https://github.com/boostorg/intrusive/commit/71317671eccb460ab33dee42f75c776310f7c3bc  
  
should silence the warning. Thanks for the report!

---

## Comment 3

> Username: cbrevdev  
> Created at: 2018-03-03 00:58:54 UTC  
> Url: https://github.com/boostorg/intrusive/issues/29#issuecomment-370100787  

Just encountered the same warning with gcc 7.2.1:  
  
```  
./boost/intrusive/pointer_plus_bits.hpp: In static member function ‘static boost::log::v2s_mt_posix::attribute_name::id_type boost::log::v2s_mt_posix::attribute_name::get_id_from_string(const char*)’:  
./boost/intrusive/pointer_plus_bits.hpp:87:48: warning: ‘<anonymous>’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
       n = pointer(uintptr_t(p) | (uintptr_t(n) & Mask));  
                                  ~~~~~~~~~~~~~~^~~~~~~  
```  
  
Sadly, the 7131767 commit wasn't able to suppress this warning.  
  
However, I notice that the warning is for "maybe" uninitialized:  
  
```  
#pragma GCC diagnostic ignored "-Wmaybe-uninitialized"  
```  
  
Tweaking 7131767's pragma in this way hides the warning in our case.
