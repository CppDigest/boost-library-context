# #54 - segfault with parallel_stable_sort and 100000 entries [Closed]

> Username: hendrikmuhs  
> Created at: 2021-04-21 19:39:35 UTC  
> Updated at: 2023-09-10 22:45:35 UTC  
> Closed at: 2023-09-10 22:45:35 UTC  
> Url: https://github.com/boostorg/sort/issues/54  

The following code snippet crashes for:  
  
 - parallel_stable_sort  
 - sorting 100000 entries  
  
This is really obscure: the crash happens only for a certain number of items, 10000 works fine as well as 200000, the crash seems to happen in a certain range. I don't know if there is anything else in this setup that triggers this problem.  
  
The example is a stripped down version from the original issue (https://github.com/KeyviDev/keyvi/issues/215).  
  
```  
#include <ciso646>  
#include <cstdio>  
#include <cstdlib>  
#include <ctime>  
#include <vector>  
#include <random>  
#include <algorithm>  
#include <boost/sort/parallel_stable_sort/parallel_stable_sort.hpp>  
#include <boost/sort/spinsort/spinsort.hpp>  
#include <boost/test/included/test_exec_monitor.hpp>  
#include <boost/test/test_tools.hpp>  
  
namespace bss = boost::sort;  
  
template <typename KeyT, typename ValueT>  
struct key_value_pair {  
  key_value_pair() : key(), value() {}  
  
  key_value_pair(const KeyT& k, const ValueT& v) : key(k), value(v) {}  
  
  KeyT key;  
  ValueT value;  
};  
  
struct ValueHandle final {  
  ValueHandle() : ValueHandle(0, 0, false, false) {}  
  ValueHandle(uint64_t value_idx, uint32_t weight, bool no_minimization, bool deleted)  
      : value_idx_(value_idx), weight_(weight), no_minimization_(no_minimization), deleted_(deleted) {}  
  
  uint64_t value_idx_;  
  uint32_t weight_;  
  bool no_minimization_;  
  bool deleted_;  
};  
  
using key_value_t = key_value_pair<std::string, ValueHandle>;  
using key_values_t = std::vector<key_value_t>;  
  
inline bool compare_key_value_pair(const key_value_t& lhs, const key_value_t& rhs) {  
  return lhs.key < rhs.key;  
}  
  
int test_main(int, char *[])  
{  
    std::vector<key_value_t> key_values;  
      
    // works fine for 200000  
    for (size_t i = 0; i < 100000; ++i) {  
      ValueHandle handle(0, 0, false, true);  
      key_values.push_back(key_value_t(std::to_string(i), handle));  
    }  
  
    bss::parallel_stable_sort(key_values.begin(), key_values.end(), compare_key_value_pair);  
      
    // works fine using another sort  
    //bss::spinsort(key_values.begin(), key_values.end(), compare_key_value_pair);  
  
    return 0;  
};  
```  
  
uname: Linux ...5.4.0-67-generic #75-Ubuntu SMP ... x86_64 x86_64 x86_64 GNU/Linux  
gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)

---

## Comment 1

> Username: fjtapia  
> Created at: 2021-04-22 20:20:18 UTC  
> Url: https://github.com/boostorg/sort/issues/54#issuecomment-825158076  

Let me a few days, and I will provide you a solution.  
Thanks for your interest.  
  
Francisco  
  
El mié, 21 abr 2021 a las 21:39, Hendrik Muhs ***@***.***>)  
escribió:  
  
> The following code snippet crashes for:  
>  
>    - parallel_stable_sort  
>    - sorting 100000 entries  
>  
> This is really obscure: the crash happens only for a certain number of  
> items, 10000 works fine as well as 200000, the crash seems to happen in a  
> certain range. I don't know if there is anything else in this setup that  
> triggers this problem.  
>  
> The example is a stripped down version from the original issue (  
> KeyviDev/keyvi#215 <https://github.com/KeyviDev/keyvi/issues/215>).  
>  
> #include <ciso646>  
> #include <cstdio>  
> #include <cstdlib>  
> #include <ctime>  
> #include <vector>  
> #include <random>  
> #include <algorithm>  
> #include <boost/sort/parallel_stable_sort/parallel_stable_sort.hpp>  
> #include <boost/sort/spinsort/spinsort.hpp>  
> #include <boost/test/included/test_exec_monitor.hpp>  
> #include <boost/test/test_tools.hpp>  
>  
> namespace bss = boost::sort;  
>  
> template <typename KeyT, typename ValueT>  
> struct key_value_pair {  
>   key_value_pair() : key(), value() {}  
>  
>   key_value_pair(const KeyT& k, const ValueT& v) : key(k), value(v) {}  
>  
>   KeyT key;  
>   ValueT value;  
> };  
>  
> struct ValueHandle final {  
>   ValueHandle() : ValueHandle(0, 0, false, false) {}  
>   ValueHandle(uint64_t value_idx, uint32_t weight, bool no_minimization, bool deleted)  
>       : value_idx_(value_idx), weight_(weight), no_minimization_(no_minimization), deleted_(deleted) {}  
>  
>   uint64_t value_idx_;  
>   uint32_t weight_;  
>   bool no_minimization_;  
>   bool deleted_;  
> };  
>  
> using key_value_t = key_value_pair<std::string, ValueHandle>;  
> using key_values_t = std::vector<key_value_t>;  
>  
> inline bool compare_key_value_pair(const key_value_t& lhs, const key_value_t& rhs) {  
>   return lhs.key < rhs.key;  
> }  
>  
> int test_main(int, char *[])  
> {  
>     std::vector<key_value_t> key_values;  
>  
>     // works fine for 200000  
>     for (size_t i = 0; i < 100000; ++i) {  
>       ValueHandle handle(0, 0, false, true);  
>       key_values.push_back(key_value_t(std::to_string(i), handle));  
>     }  
>  
>     bss::parallel_stable_sort(key_values.begin(), key_values.end(), compare_key_value_pair);  
>  
>     // works fine using another sort  
>     //bss::spinsort(key_values.begin(), key_values.end(), compare_key_value_pair);  
>  
>     return 0;  
> };  
>  
> uname: Linux ...5.4.0-67-generic #75-Ubuntu SMP ... x86_64 x86_64 x86_64  
> GNU/Linux  
> gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/54>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GFT6543F2VTWFQVTELTJ4SYVANCNFSM43K7VDRQ>  
> .  
>

---

## Comment 2

> Username: nigels-com  
> Created at: 2023-09-10 12:27:06 UTC  
> Url: https://github.com/boostorg/sort/issues/54#issuecomment-1712801048  

It appears that this was fixed in boost-1.77.0  
  
```  
$ git reset --hard boost-1.76.0 && g++ -Iinclude test.cpp && ./a.out   
HEAD is now at eb1ede3 Merge pull request #50 from boostorg/develop  
Running 1 test case...  
unknown location(0): fatal error: in "test_main_caller( argc_ argv )": memory access violation at address: 0x0000c011: no mapping at fault address  
  
*** 1 failure is detected in the test module "Test Program"  
  
$ git reset --hard boost-1.77.0 && g++ -Iinclude test.cpp && ./a.out   
HEAD is now at 72a3ae8 Merge pull request #57 from boostorg/develop  
Running 1 test case...  
  
*** No errors detected  
  
$ git reset --hard boost-1.83.0 && g++ -Iinclude test.cpp && ./a.out   
HEAD is now at f2a2396 Merge pull request #67 from boostorg/develop  
Running 1 test case...  
  
*** No errors detected  
```  
  
I also checked that the reproduced is valgrind-clean in develop branch.  
  
https://github.com/KeyviDev/keyvi/issues/215  
https://github.com/KeyviDev/keyvi/issues/219

---

## Comment 3

> Username: spreadsort  
> Created at: 2023-09-10 22:45:35 UTC  
> Url: https://github.com/boostorg/sort/issues/54#issuecomment-1712960808  

Thanks Nigel!
