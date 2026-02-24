# #285 - devector<>::push_front asserts after clear(). [Closed]

> Username: CarloWood  
> Created at: 2024-07-12 12:20:05 UTC  
> Updated at: 2024-07-15 08:41:48 UTC  
> Closed at: 2024-07-15 08:41:48 UTC  
> Url: https://github.com/boostorg/container/issues/285  

The following code snippet reproduces the problem:  
```  
#include <boost/container/devector.hpp>  
  
int main()  
{  
  boost::container::devector<int> dv;  
  
  dv.push_front(42);    // No assert.  
  dv.clear();  
  dv.push_front(42);    // Assertion `should_move_back(p) ? (back_free_cap < n) : (front_free_cap < n)' failed.  
}  
```  
  
Boost version: 1.83.0  
Compiler:  
  $ g++ -std=c++20 -g devector_bug.cpp  
  gcc version 14.1.1  
  $ clang++ -std=c++20 -g devector_bug.cpp  
  clang version 18.1.8  
OS: Arch

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-07-15 08:38:32 UTC  
> Url: https://github.com/boostorg/container/issues/285#issuecomment-2227974374  

Thanks for the report. The assertion is not correct, as your test case shows. I've removed it and added more code to test cases to reproduce your example.
