# #171 - deque::clear() uses undefined behaviour [Closed]

> Username: gleb-cloudius  
> Created at: 2020-11-09 08:58:05 UTC  
> Updated at: 2020-11-10 09:14:18 UTC  
> Closed at: 2020-11-10 09:08:23 UTC  
> Url: https://github.com/boostorg/container/issues/171  

This code:  
```  
#include <boost/container/deque.hpp>  
int main() {  
  boost::container::deque<int> x;  
  x.clear();  
}  
```  
compiled with clang and ubsan `clang++ -fsanitize=undefined` fails with:  
  
```  
/usr/include/boost/container/deque.hpp:1763:63: runtime error: applying non-zero offset 8 to null pointer  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/container/deque.hpp:1763:63 in   
```

---

## Comment 1

> Username: nyh  
> Created at: 2020-11-09 10:14:47 UTC  
> Url: https://github.com/boostorg/container/issues/171#issuecomment-723914384  

I can reproduce this with clang 10.0.0.1, Boost 1.69.0 (on Fedora 32).  
It appears that when the container is empty, `clear()` takes a null pointer + 1 as the beginning of a loop (a loop which will do nothing because its end is a null pointer as well...), but such arithmetic with null pointer is considered "undefined behavior" in C++.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-11-10 09:14:18 UTC  
> Url: https://github.com/boostorg/container/issues/171#issuecomment-724570944  

Many thanks for the report!
