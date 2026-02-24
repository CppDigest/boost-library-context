# #74 - vector assignment not using memcpy [Closed]

> Username: mglisse  
> Created at: 2018-06-15 22:37:50 UTC  
> Updated at: 2018-06-16 09:52:53 UTC  
> Closed at: 2018-06-16 09:52:53 UTC  
> Url: https://github.com/boostorg/container/issues/74  

Hello,  
when copy-assigning a `boost::container::vector<long>` to another one of the same size, I would expect the code to end up as a call to memcpy (or possibly memmove). However, in boost, we end up in a hand-written loop in assign:  
```c++  
      for ( ; first != last && cur != end_it; ++cur, ++first){  
         *cur = *first;  
      }  
```  
This makes the following crude benchmark 4-5 times slower than using `std::vector` from libstdc++ or libc++.  
```c++  
#include <vector>  
#include <boost/container/vector.hpp>  
int main(int argc,char**){  
#if 1  
  typedef boost::container::vector<long> V;  
#else  
  typedef std::vector<long> V;  
#endif  
  V m(1024),n(1024);  
  for(int k=0;k<1000000;++k) {  
    n=m;  
    m=n;  
  }  
  return m[argc];  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-06-16 09:52:53 UTC  
> Url: https://github.com/boostorg/container/issues/74#issuecomment-397801655  

Thanks for the report. Fixed in commit:  
  
https://github.com/boostorg/container/commit/62ee7403687849cc50654ad6870faceb03f8662f
