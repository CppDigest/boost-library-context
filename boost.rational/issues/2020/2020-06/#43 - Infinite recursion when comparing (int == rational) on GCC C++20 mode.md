# #43 - Infinite recursion when comparing (int == rational) on GCC C++20 mode [Closed]

> Username: nyanpasu64  
> Created at: 2020-06-28 16:09:12 UTC  
> Updated at: 2021-05-06 04:24:15 UTC  
> Closed at: 2020-11-16 16:35:27 UTC  
> Url: https://github.com/boostorg/rational/issues/43  

```cpp  
#include <boost/rational.hpp>  
  
int main() {  
    int b = 0;  
    boost::rational<int> a{0};  
    b == a;  
  
    return 0;  
}  
```  
  
This code invokes `operator == (const int& b, const rational<int>& a)` listed on https://github.com/boostorg/rational/blob/47c1b26964b06a99200769474d58a309267c50f1/include/boost/rational.hpp#L773-L780. This calls `a == b`, swapping the order of the arguments.  
  
When compiled in C++17 mode, this delegates to `rational<int>::operator==(const int& i)` (not sure about the exact signature).  
  
When compiled in C++20 mode on GCC, the method recurses indefinitely. `gcc (SUSE Linux) 10.1.1 20200507 [revision dd38686d9c810cecbaa80bb82ed91caaa58ad635]`  
  
On Clang, there's no such issue. `clang version 10.0.0, x86_64-unknown-linux-gnu`  
  
I haven't tested on Windows and MSVC yet.  
  
I suspect this is related to P1185R2. But according to https://en.cppreference.com/w/cpp/compiler_support, both gcc 10 and clang 10 have implemented this, but only GCC has infinite recursion.  
  
I'm on openSUSE Tumbleweed 20200626.

---

## Comment 1

> Username: nyanpasu64  
> Created at: 2020-07-14 11:00:44 UTC  
> Url: https://github.com/boostorg/rational/issues/43#issuecomment-658116141  

I pasted the above code into Wandbox with GCC 10.1.0. It produces a descriptive warning calling out this exact issue:  
  
`/opt/wandbox/boost-1.73.0/gcc-10.1.0/include/boost/rational.hpp:779:16: warning: in C++20 this comparison calls the current function recursively with reversed arguments`

---

## Comment 2

> Username: glenfe  
> Created at: 2020-11-16 14:33:17 UTC  
> Url: https://github.com/boostorg/rational/issues/43#issuecomment-728100091  

I've fixed this in #45. I'll close this bug once I've merged to master.

---

## Comment 3

> Username: glenfe  
> Created at: 2020-11-16 16:35:27 UTC  
> Url: https://github.com/boostorg/rational/issues/43#issuecomment-728176450  

Tests on Travis and Appveyor were green for the PR. Merged to master for Boost 1.75
