# #291 - Compilation errors with gcc 4.9 through 7 due to unknown `no_sanitize` attribute [Closed]

> Username: Lastique  
> Created at: 2023-08-25 15:00:43 UTC  
> Updated at: 2023-10-22 18:21:29 UTC  
> Closed at: 2023-10-22 18:21:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/291  

Gcc from 4.9 to 7, inclusively, fail to compile code using Boost.Serialization because of the unsupported `no_sanitize` attribute used in `boost/archive/detail/common_oarchive.hpp`. This has manifested as Boost.Core CI [failures](https://github.com/boostorg/core/actions/runs/5975512799/job/16211622790#step:8:589) like this:  
  
```  
In file included from ./boost/archive/detail/common_oarchive.hpp:22:0,  
                 from ./boost/archive/basic_xml_oarchive.hpp:22,  
                 from ./boost/archive/xml_oarchive.hpp:31,  
                 from libs/core/test/serialization_nvp_test.cpp:24:  
./boost/archive/detail/interface_oarchive.hpp:54:20: error: 'no_sanitize' attribute directive ignored [-Werror=attributes]  
     Archive * This(){  
                    ^  
cc1plus: all warnings being treated as errors  
  
    "g++-4.9"   -std=c++03 -fvisibility-inlines-hidden -fPIC -m32 -pthread -O0 -fno-inline -Wall -Wextra -Werror -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1   -I"."  -c -o "bin.v2/libs/core/test/serialization_nvp_test.test/gcc-4.9/debug/address-model-32/cxxstd-03-iso/threading-multi/visibility-hidden/serialization_nvp_test.o" "libs/core/test/serialization_nvp_test.cpp"  
```  
  
This regression was introduced by be9421f80ff5e8dedaf9bdf0c6b84a7aad3d10b6. CC @mborland.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-08-25 15:14:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/291#issuecomment-1693524401  

BTW, the clang check in the referenced commit should probably be replaced with `__has_attribute`.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-08-25 17:37:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/291#issuecomment-1693715220  

https://github.com/boostorg/serialization/pull/292 should be the better fix, although I see CI failures there.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-08-25 17:40:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/291#issuecomment-1693718997  

Oh, those are because of C++03.

---

## Comment 4

> Username: robertramey  
> Created at: 2023-10-21 20:36:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/291#issuecomment-1773915885  

can I close this now?

---

## Comment 5

> Username: pdimov  
> Created at: 2023-10-21 22:38:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/291#issuecomment-1773937954  

I think so, yes.
