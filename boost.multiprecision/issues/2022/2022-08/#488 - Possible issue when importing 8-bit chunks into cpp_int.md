# #488 - Possible issue when importing 8-bit chunks into cpp_int [Closed]

> Username: ckormanyos  
> Created at: 2022-08-29 12:59:17 UTC  
> Updated at: 2022-08-30 12:12:04 UTC  
> Closed at: 2022-08-30 12:12:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/488  

Well, I ran into the following phenomenon today.  
  
While doing some in-depth testing of `cpp_int`'s facility for `import_bits()`, I found what seems to be a potential bug. It is only in GCC, not in MSVC (did not try clang yet).  
  
Anyway, when importing a `chunk_size` or 8 from an array of 16-bit unsigned integers, **and** only for `msv_first` being `false`, the import function seems to be optimized and imports too large of chunks.  
  
I would like to retain the optimization, but maybe it needs improved logic. It seems to be independent of singed/unsigned versions of `char`. Yet I can't quite figure out why MSVC gets it OK, but GCC does not.  
  
The entire code is below.  
The link to Wandbox is [here](https://wandbox.org/permlink/aPuF8FyJU4X1IQpR).  
  
```c++  
#include <array>  
#include <cstdint>  
#include <iomanip>  
#include <iostream>  
  
#include <boost/multiprecision/cpp_int.hpp>  
  
// cd /mnt/c/Users/User/Documents/Ks/PC_Software/Test  
// g++-10 -march=native -mtune=native -O3 -Wall -Wextra -std=c++20 -I/mnt/c/boost/boost_1_80_0 test.cpp -o test.exe  
  
auto main() -> int  
{  
  using local_uint265_type = boost::multiprecision::uint256_t;  
  
  using bits_array_type = std::array<std::uint16_t, static_cast<std::size_t>(UINT8_C(16))>;  
  
  bits_array_type bits { };  
  
  bits.fill(static_cast<std::uint16_t>(UINT16_C(0x5678)));  
  
  local_uint265_type u { };  
  
  const std::array<bool, static_cast<std::size_t>(UINT8_C(2))> msv_values { true, false };  
  
  const auto flg = std::cout.flags();  
  
  for(const auto msv_first : msv_values)  
  {  
    static_cast<void>(import_bits(u, bits.cbegin(), bits.cend(), static_cast<unsigned>(UINT8_C(8)), msv_first));  
  
    std::cout << std::hex << std::uppercase << u << std::endl;  
  }  
  
  std::cout.flags(flg);  
  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-08-29 13:00:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/488#issuecomment-1230255091  

It is present in 1.80 and on develop as well.  
Seems also like the code has been around for a while.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-08-29 16:29:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/488#issuecomment-1230548582  

I have a trivial fix, but need to devise a portable test case (which is somewhat harder).  
  
The issue is triggered by std::array<>::const_iterator being a pointer in gcc, *and* the fact that you're only importing the first byte of each value and not the whole thing, it's a check for the latter which is missing in the gcc case.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2022-08-29 19:54:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/488#issuecomment-1230794277  

Thank you John!
