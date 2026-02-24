# #30 - Ambiguity related to cpp_int -> cpp_rational [Closed]

> Username: mglisse  
> Created at: 2017-07-20 15:18:17 UTC  
> Updated at: 2017-12-26 17:01:02 UTC  
> Closed at: 2017-12-26 17:01:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/30  

Hello,  
  
I don't know if this is really a bug, but let's say it is inconvenient. The following program  
```c++  
#include <boost/multiprecision/cpp_int.hpp>  
struct E { E(boost::multiprecision::cpp_rational const&); };  
int main(){  
  boost::multiprecision::cpp_int r = 3;  
  E x(r);  
}  
```  
fails to compile with g++:  
  
> b.cc: In function ‘int main()’:  
> b.cc:5:8: error: call of overloaded ‘E(boost::multiprecision::cpp_int&)’ is ambiguous  
>    E x(r);  
>         ^  
> b.cc:2:12: note: candidate: E::E(const cpp_rational&)  
>  struct E { E(boost::multiprecision::cpp_rational const&); };  
>             ^  
> b.cc:2:8: note: candidate: constexpr E::E(const E&)  
>  struct E { E(boost::multiprecision::cpp_rational const&); };  
>         ^  
> b.cc:2:8: note: candidate: constexpr E::E(E&&)  
  
(clang++ gives a similar error message)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-12-26 17:01:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/30#issuecomment-353989878  

This should now be fixed in https://github.com/boostorg/multiprecision/commit/4daa1baf6d5eaaf16bfc6294e1ac3dd0e051de3f.
