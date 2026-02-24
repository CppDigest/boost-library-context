# #32 - include/boost/integer_traits.hpp:83:64: error: narrowing conversion of '255' from 'int' to 'char' [-Wnarrowing] [Closed]

> Username: ggardet  
> Created at: 2022-05-16 09:00:18 UTC  
> Updated at: 2022-05-17 11:10:30 UTC  
> Closed at: 2022-05-17 11:09:23 UTC  
> Url: https://github.com/boostorg/integer/issues/32  

While trying to build ceph with GCC12 in openSUSE Tumbleweed aarch64, we get an error on boost header:  
```  
[14382s] [ 58%] Building CXX object src/rgw/CMakeFiles/rgw_common.dir/rgw_tag_s3.cc.o  
[14397s] In file included from /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/build/boost/include/boost/integer.hpp:20,  
[14397s]                  from /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/build/boost/include/boost/integer/integer_mask.hpp:16,  
[14397s]                  from /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/build/boost/include/boost/random/mersenne_twister.hpp:26,  
[14397s]                  from /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/build/boost/include/boost/uuid/random_generator.hpp:17,  
[14397s]                  from /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/build/boost/include/boost/uuid/uuid_generators.hpp:17,  
[14397s]                  from /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/src/include/uuid.h:16,  
[14397s]                  from /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/src/include/types.h:21,  
[14397s]                  from /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/src/ceph_mgr.cc:21:  
[14397s] /home/abuild/rpmbuild/BUILD/ceph-16.2.7-654-gd5a90ff46f0/build/boost/include/boost/integer_traits.hpp:83:64: error: narrowing conversion of '255' from 'int' to 'char' [-Wnarrowing]  
[14397s]    83 |     public detail::integer_traits_base<char, CHAR_MIN, CHAR_MAX>  
[14397s]       |                                                                ^  
```  
  
x86 is fine, so this is likely an issue with char being by default signed or unsigned, depending on architecture.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-16 11:23:06 UTC  
> Updated at: 2022-05-16 11:23:24 UTC  
> Url: https://github.com/boostorg/integer/issues/32#issuecomment-1127548509  

`CHAR_MIN` and `CHAR_MAX` must be defined to constants that fit in the range of `char`, so if there is narrowing happening then this must be a compiler or C standard library bug.  
  
Can you verify that the constants have valid values, according to your target `char` definition?

---

## Comment 2

> Username: ggardet  
> Created at: 2022-05-16 13:03:13 UTC  
> Url: https://github.com/boostorg/integer/issues/32#issuecomment-1127645454  

On my aarch64 system, I have `echo -en '#include <climits>' | g++ -dM -x c++ -E - | grep CHAR_MAX$` which returns `#define CHAR_MAX UCHAR_MAX`

---

## Comment 3

> Username: Lastique  
> Created at: 2022-05-16 13:24:43 UTC  
> Url: https://github.com/boostorg/integer/issues/32#issuecomment-1127671448  

Yes, but that's not what I'm asking. Is `char` signed or not? Do `CHAR_MIN` and `CHAR_MAX` actually fit in it?

---

## Comment 4

> Username: ggardet  
> Created at: 2022-05-16 13:56:15 UTC  
> Url: https://github.com/boostorg/integer/issues/32#issuecomment-1127708381  

On Arm, `char` should be unsigned.

---

## Comment 5

> Username: ggardet  
> Created at: 2022-05-17 11:09:19 UTC  
> Updated at: 2022-05-17 11:10:30 UTC  
> Url: https://github.com/boostorg/integer/issues/32#issuecomment-1128735749  

Closing as this is a GCC12/python issue: https://bugs.python.org/issue46513
