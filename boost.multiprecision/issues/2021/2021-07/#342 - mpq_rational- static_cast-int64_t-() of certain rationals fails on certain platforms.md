# #342 - mpq_rational: static_cast<int64_t>() of certain rationals fails on certain platforms [Closed]

> Username: mbunkus  
> Created at: 2021-07-04 18:26:39 UTC  
> Updated at: 2021-07-07 08:45:30 UTC  
> Closed at: 2021-07-07 08:45:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/342  

The short version: converting a `mpq_rational{10, 3}` to an `int64_t` via `static_cast<int64_t>(…)` yields `std:::numeric_limits<int64_t>::max()` on at least the following platforms:  
  
* Debian Buster x86 32-bit (Boost 1.67, gmp 6.1.2)  
* A mingw cross compiler from the MXE project (gcc 10.2, Boost 1.74, gmp 6.1.2) in both x86 32-bit (`i686-w64-mingw32.static-g++`) and x86 64-bit (`x86_64-w64-mingw32.static-g++`) modes  
* Raspberry Pi OS (which is based on Debian Buster; Boost 1.67, gmp 6.1.2) on a Raspberry Pi 4 (`armhf` architecture)  
  
It works fine and returns `3` on all the other platforms I could test, which are:  
  
* Debian Buster 64-bit (Boost 1.67, gmp 6.1.2); yeah same OS, just 64-bit instead of 32-bit  
* Ubuntu Focal 64-bit (Boost 1.71, gmp 6.2.0)  
* AlmaLinux 64-bit (Boost 1.66, gmp  
* Arch 64-bit (Boost 1.76, gmp 6.2.1)  
  
In summary: it _fails_ on all 32-bit platforms and on Windows in general. It _works_ on 64-bit Linux platforms. Both seem to be independent of the versions of the libraries involved.  
  
**However**, the conversion works if I convert to `boost::multiprecision::int128_t` first, then to `int64_t`. See the following sample code:  
  
```c++  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/gmp.hpp>  
  
int main(int, char **) {  
  boost::multiprecision::mpq_rational value{10, 3};  
  
  std::cout << "fails: " << static_cast<int64_t>(value) << "\n";  
  std::cout << "works: " << static_cast<int64_t>(static_cast<boost::multiprecision::int128_t>(value)) << "\n";  
  
  return 0;  
}  
  
```  
  
Compile with e.g.:  
  
```sh  
g++ -Wall -Wextra -std=c++17 -o test test.cpp -lgmp  
  
```  
  
Sample output on an affected platform:  
  
``` shell  
$ ./test  
fails: 9223372036854775807  
works: 3  
```  
  
It should print `3` in both lines.  
  
Things I've tried unsuccessfully:  
  
* Compiling with or without optimizations (`-O3`)  
* Compiling with different C++ standard versions  
* Using different gcc versions  
* Using clang  
  
Things that do work, even on the aforementioned affected platforms:  
  
* Converting `mpq_rational{0, 1}`  
* Converting `mpq_rational{10, 1}`  
* Converting `boost::multiprecision::mpq_rational{boost::multiprecision::mpq_rational{std::numeric_limits<int64_t>::max()}, boost::multiprecision::mpq_rational{1}} - 1`  
  
I obviously have no clue why the conversion fails.  
  
Background: I'm trying to convert my program from using `boost::rational<int64_t>` to `boost::multiprecision::mpq_rational`. If I understand the documentation correctly, the equivalent of a `boost::rational_cast<int64_t>()` ("convert this rational to the closest (truncated) `int64_t` value") is a regular `static_cast<int64_t>()`. If not, please point me in the right direction. Thanks!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-07-06 07:53:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/342#issuecomment-874543425  

Confirmed, I see the problem.
