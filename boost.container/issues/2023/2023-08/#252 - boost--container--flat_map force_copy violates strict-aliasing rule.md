# #252 - boost::container::flat_map force_copy violates strict-aliasing rule [Closed]

> Username: prus1337  
> Created at: 2023-08-07 16:15:36 UTC  
> Updated at: 2024-06-06 21:01:35 UTC  
> Closed at: 2024-06-06 21:01:34 UTC  
> Url: https://github.com/boostorg/container/issues/252  

Hi everyone!  
  
g++ v13.1.0, v13.2.0, boost v1.82, MSYS2 MINGW64_NT-10.0-19044.   
  
Discussion links:  
https://github.com/msys2/MINGW-packages/issues/17977  
https://github.com/msys2/MINGW-packages/issues/17977#issuecomment-1668114318  
  
The following example has different behavior depending on compiler optimization flags:  
```C++  
#include <boost/container/flat_map.hpp>  
  
#include <cstdint>  
#include <iostream>  
#include <string>  
  
int main()  
{  
    boost::container::flat_map<std::string, size_t> map{};  
    const std::string key = "test";  
    const size_t value = 13;  
	  
    auto [it, succeeded] = map.emplace(key, value);  
	  
    if (succeeded)  
    {  
        std::cout << "emplace result: " << it->first << " -> " << it->second << "\n";  
        std::cout << "emplace iterator result: " << "it == map.end() is " << std::boolalpha << (it == map.end()) << "\n";  
		  
        it = map.find(key);  
        std::cout << "find result: " << it->first << " -> " << it->second << "\n";  
        std::cout << "find iterator result: " << "it == map.end() is " << std::boolalpha << (it == map.end()) << "\n";   
    }  
	  
    return 0;  
}  
```  
### Expected behavior  
Without optimization flags (-O0, -O1) everything fine.  
g++ -O0 -o boost_v1.82_flat_map_it_bug boost_v1.82_flat_map_it_bug.cpp  
  
> $ ./boost_v1.82_flat_map_it_bug  
> emplace result: test -> 13  
> emplace iterator result: it == map.end() is false  
> find result: test -> 13  
> find iterator result: it == map.end() is false  
  
### Actual behavior  
With optimization flags (-O2, -O3) iterator has bad value.  
g++ -O2 -o boost_v1.82_flat_map_it_bug boost_v1.82_flat_map_it_bug.cpp  
  
> ./boost_v1.82_flat_map_it_bug  
> emplace result: test -> 13  
> emplace iterator result: it == map.end() is true  
> find result: test -> 13  
> find iterator result: it == map.end() is true  
  
The issue does not happen in Linux with gcc 13.2.1 toolchain.

---

## Comment 1

> Username: Mark-MnetCS  
> Created at: 2023-12-20 21:02:27 UTC  
> Url: https://github.com/boostorg/container/issues/252#issuecomment-1865133771  

I bumped into a version of this problem that's reproducible on GCC trunk  
  
  ```c++  
  #include <utility>  
  #include <boost/container/flat_map.hpp>  
    
  namespace bc = boost::container;  
    
  int main() {  
      bc::flat_map<int, int> map {  
          std::pair(0, 1),  
          std::pair(1, 2),  
          std::pair(2, 4)  
      };  
    
      if (map.size() != 3) {  
          return 1;  
      }  
    
      int i = 0;  
      for (const auto &[k, v] : map) {  
          ++i;  
      }  
      if (i != 3) {  
          return 2;  
      }  
    
      return 0;  
  }  
```  
  
When this is compiled with `-O1` it runs as expected: `i == 3` and the exit status is 0.   
When compiled with `-DBOOST_CONTAINER_DISABLE_FORCEINLINE -O2` the loop gets optimized away, `i == 0` and the exit status is 2.  
  
Here's a Compiler Explorer link showing the result: https://godbolt.org/z/7W5eGrKGE  
Built with  `g++ (Compiler-Explorer-Build-gcc-181917d56c0cf4b8c1174a492a8157f4a12d1100-binutils-2.40) 14.0.0 20231219 (experimental)`

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-06-06 21:01:34 UTC  
> Url: https://github.com/boostorg/container/issues/252#issuecomment-2153402168  

Thanks for the report. The strict aliasing violation was needed only for ancient compilers so the following commit removes it for C++11 o newer:  
  
https://github.com/boostorg/container/commit/20ad12f20e661978e90dc7f36d8ab8ac05e5a5a9
