# #760 - gil lib failed to build due to many errors on Windows with MSVC [Closed]

> Username: LinWrSpace  
> Created at: 2025-04-03 06:04:35 UTC  
> Updated at: 2025-04-04 17:48:45 UTC  
> Closed at: 2025-04-04 16:27:31 UTC  
> Url: https://github.com/boostorg/gil/issues/760  

**Issue Description:**  
Found Boost\libs\gil failed to build due to many errors on Windows with MSVC, this issue can be reproduced on 6f50c3c commit of Boost master branch. Could you please take a look? Thanks in advance.  
  
**Reproduce Steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git f:\Boost  
2. open a VS 2022 x64 command prompt and browse to f:\Boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
5. .\b2 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
6. .\b2 -j16 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 libs\gil\test address-model=64  
  
[gil_test.log](https://github.com/user-attachments/files/19580906/gil_test.log)  
  
**ErrorMessage:**  
  
```  
compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\gil\test\core\h-56-io-bit_operations.test\msvc-14.3\release\x86_64\threading-multi\h-56-io-bit_operations.obj  
main.cpp  
.\boost/gil/io/typedefs.hpp(34): error C3856: 'is_floating_point': symbol is not a class template  
.\boost/gil/io/typedefs.hpp(34): error C2143: syntax error: missing ';' before 'boost::gil::float32_t'  
.\boost/gil/io/typedefs.hpp(34): error C2913: explicit specialization; 'boost::is_floating_point' is not a specialization of a class template  
.\boost/gil/io/typedefs.hpp(34): error C2059: syntax error: '>'  
.\boost/gil/io/typedefs.hpp(34): error C2059: syntax error: 'function-style cast'  
.\boost/gil/io/typedefs.hpp(34): error C2143: syntax error: missing ';' before '{'  
.\boost/gil/io/typedefs.hpp(34): error C2447: '{': missing function header (old-style formal list?)  
.\boost/gil/io/typedefs.hpp(35): error C2913: explicit specialization; 'boost::is_floating_point' is not a specialization of a class template  
...  
...failed updating 4 targets...  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\gil\test\core\h-56-io-bit_operations.test\msvc-14.3\release\x86_64\threading-multi\h-56-io-bit_operations.obj  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\gil\test\core\h-78-io-row_buffer_helper.test\msvc-14.3\release\x86_64\threading-multi\h-78-io-row_buffer_helper.obj  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\gil\test\core\h-79-io-scanline_read_iterator.test\msvc-14.3\release\x86_64\threading-multi\h-79-io-scanline_read_iterator.obj  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\gil\test\core\h-80-io-typedefs.test\msvc-14.3\release\x86_64\threading-multi\h-80-io-typedefs.obj  
```

---

## Comment 1

> Username: sdebionne  
> Created at: 2025-04-04 14:29:24 UTC  
> Url: https://github.com/boostorg/gil/issues/760#issuecomment-2778897690  

Indeed, the code was modernized to use the standard `<type_traits>` but in `boost/gil/io/typedefs.hpp` we still have a specialization of `boost::is_floating_point<...>`:  
  
```c++  
namespace boost {  
  
template<> struct is_floating_point<gil::float32_t> : std::true_type {};  
template<> struct is_floating_point<gil::float64_t> : std::true_type {};  
  
} // namespace boost  
```  
  
Fixing that now.

---

## Comment 2

> Username: striezel  
> Created at: 2025-04-04 17:48:44 UTC  
> Url: https://github.com/boostorg/gil/issues/760#issuecomment-2779405784  

> Found Boost\libs\gil failed to build due to many errors on Windows with MSVC, this issue can be reproduced on 6f50c3c commit of Boost master branch.  
  
Note to myself: This is the commit: https://github.com/boostorg/boost/commit/6f50c3c5b51972fdb4e022a513caafc92f4af158
