# #171 - Extract throw_exception calls into noinline function(s) [Closed]

> Username: pdimov  
> Created at: 2022-11-28 17:30:34 UTC  
> Updated at: 2023-12-19 16:08:09 UTC  
> Closed at: 2023-12-19 16:08:09 UTC  
> Url: https://github.com/boostorg/unordered/issues/171  

E.g. here https://github.com/boostorg/unordered/blob/d0369942373abb64eef1459a8c0ddd8af6436dfd/include/boost/unordered/unordered_flat_map.hpp#L407-L418  
  
Since the call to `boost::throw_exception` generates a fair bit of code and is typically "cold", we want this extracted into a separate BOOST_NOINLINE function so that the rest of `at` can be inlined.  
  
In addition, we also typically want to either pass BOOST_CURRENT_LOCATION to `boost::throw_exception`, or switch to `boost::throw_with_location` (again with supplying a location to it).  
  
As a further refinement, it's possible to add a default source_location argument to `at` so that the location in the exception ends up being the point of calling `at`, instead of the point inside of `at`. This however changes the signature of `at`, so it should be a separate change, should we decide to do it.

---

## Comment 1

> Username: k3DW  
> Created at: 2023-12-18 03:39:02 UTC  
> Updated at: 2023-12-18 03:50:05 UTC  
> Url: https://github.com/boostorg/unordered/issues/171#issuecomment-1859501475  

Here are my test source files to see the effects of this change.  
```cpp  
#include <boost/unordered_map.hpp>  
using key_type = ???;  
using map_type = boost::unordered_map<key_type, int>;  
  
// Emulation of the old behaviour  
int const& at_old(map_type const& map, key_type key)  
{  
    auto it = map.find(key);  
    if(it != map.end()) return it->second;  
  
    boost::throw_exception(std::out_of_range("key not found"));  
}  
  
int main()  
{  
    map_type map;  
    at_old(map, key_type{});  
}  
```  
  
```cpp  
#include <boost/unordered_map.hpp>  
using key_type = ???;  
using map_type = boost::unordered_map<key_type, int>;  
  
BOOST_NORETURN BOOST_NOINLINE  
void throw_out_of_range()  
{  
    boost::throw_exception(std::out_of_range("key not found"));  
}  
  
// Emulation of the new behaviour  
int const& at_new(map_type const& map, key_type key)  
{  
    auto it = map.find(key);  
    if(it != map.end()) return it->second;  
  
    throw_out_of_range();  
}  
  
int main()  
{  
    map_type map;  
    at_new(map, key_type{});  
}  
```  
  
For example, with `using key_type = int;` on gcc-13.2 with `-O2`, we get this https://godbolt.org/z/Ga6WGYdn1.  
  
Here are the number of lines of assembly for the `at_old()` or `at_new()` function shown in the compiler pane, for each case with `using key_type = int;`. These are the number of lines for the entirety of the function.  
```  
gcc-13.2 -O2 => at_old = 178 lines  
gcc-13.2 -O2 => at_new = 15 lines  
gcc-13.2 -O3 => at_old = 72 lines  
gcc-13.2 -O3 => at_new = 41 lines  
clang-17.0.1 -O2 => at_old = 73 lines  
clang-17.0.1 -O2 => at_new = 43 lines  
clang-17.0.1 -O3 => at_old = 73 lines  
clang-17.0.1 -O3 => at_new = 43 lines  
msvc-x64-19.38 /O2 => at_old = 60 lines  
msvc-x64-19.38 /O2 => at_new = 55 lines  
msvc-x64-19.38 /Ox => at_old = 60 lines  
msvc-x64-19.38 /Ox => at_new = 55 lines  
msvc-x86-19.38 /O2 => at_old = 82 lines  
msvc-x86-19.38 /O2 => at_new = 41 lines  
msvc-x86-19.38 /Ox => at_old = 82 lines  
msvc-x86-19.38 /Ox => at_new = 41 lines  
```

---

## Comment 2

> Username: k3DW  
> Created at: 2023-12-18 04:05:23 UTC  
> Url: https://github.com/boostorg/unordered/issues/171#issuecomment-1859516814  

For `using key_type = std::pair<int, int>;`  
```  
gcc-13.2 -O2 => at_old = 46 lines  
gcc-13.2 -O2 => at_new = 15 lines  
gcc-13.2 -O3 => at_old = 108 lines  
gcc-13.2 -O3 => at_new = 75 lines  
clang-17.0.1 -O2 => at_old = 28 lines  
clang-17.0.1 -O2 => at_new = 16 lines  
clang-17.0.1 -O3 => at_old = 28 lines  
clang-17.0.1 -O3 => at_new = 16 lines  
msvc-x64-19.38 /O2 => at_old = 31 lines  
msvc-x64-19.38 /O2 => at_new = 26 lines  
msvc-x64-19.38 /Ox => at_old = 31 lines  
msvc-x64-19.38 /Ox => at_new = 26 lines  
msvc-x86-19.38 /O2 => at_old = 110 lines  
msvc-x86-19.38 /O2 => at_new = 70 lines  
msvc-x86-19.38 /Ox => at_old = 110 lines  
msvc-x86-19.38 /Ox => at_new = 70 lines  
```  
<br>  
  
For `using key_type = std::string_view;`  
```  
gcc-13.2 -O2 => at_old = 47 lines  
gcc-13.2 -O2 => at_new = 16 lines  
gcc-13.2 -O3 => at_old = 169 lines  
gcc-13.2 -O3 => at_new = 136 lines  
clang-17.0.1 -O2 => at_old = 34 lines  
clang-17.0.1 -O2 => at_new = 22 lines  
clang-17.0.1 -O3 => at_old = 34 lines  
clang-17.0.1 -O3 => at_new = 22 lines  
msvc-x64-19.38 /O2 => at_old = 30 lines  
msvc-x64-19.38 /O2 => at_new = 25 lines  
msvc-x64-19.38 /Ox => at_old = 30 lines  
msvc-x64-19.38 /Ox => at_new = 25 lines  
msvc-x86-19.38 /O2 => at_old = 70 lines  
msvc-x86-19.38 /O2 => at_new = 32 lines  
msvc-x86-19.38 /Ox => at_old = 70 lines  
msvc-x86-19.38 /Ox => at_new = 32 lines  
```
