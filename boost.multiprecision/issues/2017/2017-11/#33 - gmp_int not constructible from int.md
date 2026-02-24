# #33 - gmp_int not constructible from int [Closed]

> Username: mglisse  
> Created at: 2017-11-29 16:55:37 UTC  
> Updated at: 2017-11-29 17:02:40 UTC  
> Closed at: 2017-11-29 17:02:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/33  

```c++  
#include <boost/multiprecision/gmp.hpp>  
int main(){  
  boost::multiprecision::gmp_int n(0);  
}  
```  
Fails to compile as ambiguous. Replacing 0 with 42, we get that there is no matching constructor.  
  
> As well as the usual conversions from arithmetic and string types, type mpz_int is copy constructible and assignable from [...]  
  
This is from the documentation, so I was expecting gmp_int to be constructible from int...

---

## Comment 1

> Username: mglisse  
> Created at: 2017-11-29 17:02:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/33#issuecomment-347926900  

Ah no oops that's the name of the backend, not the number type, sorry.
