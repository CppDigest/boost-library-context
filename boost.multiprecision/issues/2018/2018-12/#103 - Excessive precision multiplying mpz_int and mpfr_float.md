# #103 - Excessive precision multiplying mpz_int and mpfr_float [Closed]

> Username: vbeffara  
> Created at: 2018-12-20 21:34:55 UTC  
> Updated at: 2019-03-04 18:33:39 UTC  
> Closed at: 2019-03-04 18:33:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/103  

Trying boost 1.69 on previously working code (with clang 7 on OSX), I ended up with the following weird behavior.  
  
```c++  
#include <boost/multiprecision/gmp.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <iostream>  
  
using boost::multiprecision::mpfr_float;  
using boost::multiprecision::mpz_int;  
  
int main() {  
    mpfr_float::default_precision(100);  
    std::cerr << mpfr_float{mpz_int{1} * mpfr_float{1}}.precision() << "\n";  
    std::cerr << mpfr_float{mpfr_float{mpz_int{1}} * mpfr_float{1}}.precision() << "\n";  
}  
```  
  
The second output line says 100 which is what I expected; the first one says 646392383 and takes about 2 seconds, which is surprising.  
  
This likely has to do with the "big update to better support variable precision types so that the precision of the result is always the largest of all the arguments" plus the fact that mpz_int would be understood as having infinite precision, but in that case it is not the intuitive behavior.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-03-04 18:33:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/103#issuecomment-469365071  

Fixed and merged to master.
