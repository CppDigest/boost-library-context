# #165 Fix more sign conversion errors [Merged]

> Username: k3DW  
> Created at: 2024-08-19 00:42:36 UTC  
> Updated at: 2024-08-19 22:34:09 UTC  
> Merged at: 2024-08-19 22:20:38 UTC  
> Closed at: 2024-08-19 22:20:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/165  

These are some more errors I'm seeing in the Unordered tests in [unordered#274](https://github.com/boostorg/unordered/pull/274). This is related to #162 / #163.  
  
I reproduced it locally with GCC 9 with the command used in the CI.  
```  
"g++-9"   -std=c++11 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -Wextra -pedantic -Werror -fvisibility=hidden -Wconversion -Wfloat-equal -Wno-variadic-macros -Wshadow -Wsign-conversion -Wsign-promo -DBOOST_ALL_NO_LIB=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/unordered/test/visualization_tests.test/gcc-9/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/visualization_tests.o" "libs/unordered/test/debuggability/visualization_tests.cpp"  
```  
Here is a modification of the command that produces the same error on my machine, using only the Uuid library.  
```  
g++-9 -Werror -Wsign-conversion -I /mnt/d/boost libs/uuid/include/boost/uuid/uuid_io.hpp  
```  
```  
In file included from libs/uuid/include/boost/uuid/uuid_io.hpp:9:  
/mnt/d/boost/boost/uuid/uuid.hpp: In member function ‘uint8_t* boost::uuids::uuid::end()’:  
/mnt/d/boost/boost/uuid/uuid.hpp:124:49: error: conversion to ‘long int’ from ‘boost::uuids::uuid::size_type’ {aka ‘long unsigned int’} may change the sign of the result [-Werror=sign-conversion]  
  124 |     iterator end() noexcept { return data + size(); }  
      |                                             ~~~~^~  
/mnt/d/boost/boost/uuid/uuid.hpp: In member function ‘const uint8_t* boost::uuids::uuid::end() const’:  
/mnt/d/boost/boost/uuid/uuid.hpp:125:61: error: conversion to ‘long int’ from ‘boost::uuids::uuid::size_type’ {aka ‘long unsigned int’} may change the sign of the result [-Werror=sign-conversion]  
  125 |     const_iterator end() const noexcept { return data + size(); }  
      |                                                         ~~~~^~  
In file included from libs/uuid/include/boost/uuid/uuid_io.hpp:10:  
/mnt/d/boost/boost/uuid/detail/to_chars.hpp: In function ‘Ch* boost::uuids::detail::to_chars(const boost::uuids::uuid&, Ch*)’:  
/mnt/d/boost/boost/uuid/detail/to_chars.hpp:50:35: error: conversion to ‘long int’ from ‘std::size_t’ {aka ‘long unsigned int’} may change the sign of the result [-Werror=sign-conversion]  
   50 |         std::uint8_t ch = u.data[ i ];  
      |                                   ^  
cc1plus: all warnings being treated as errors  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-08-19 06:40:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/165#issuecomment-2295779049  

The `operator()` change is incorrect.

---

## Comment 2

> Username: k3DW  
> Created_at: 2024-08-19 13:06:35 UTC  
> Url: https://github.com/boostorg/uuid/pull/165#issuecomment-2296535773  

> The `operator()` change is incorrect.  
  
I'll change it back.

---
