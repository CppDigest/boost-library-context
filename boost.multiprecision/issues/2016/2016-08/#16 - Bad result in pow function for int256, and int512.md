# #16 - Bad result in pow function for int256, and int512 [Closed]

> Username: alfC  
> Created at: 2016-08-08 02:00:32 UTC  
> Updated at: 2017-03-05 18:59:56 UTC  
> Closed at: 2017-03-05 18:59:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/16  

`checked_int256` gives the wrong result of some `pow` operations without giving overflow.  
  
I couldn't reproduce the error with `checked_int128`. But I find a similar behavior for `checked_int256` between `i = 47` and `i = 54` (for 55 it overflows), and also find this problem with `checked_int512` for between `i = 81` and `i = 84` (for 85 overflows).  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
using std::cout;  
  
int main(){  
  
    cout << pow(boost::multiprecision::checked_int256_t(46), 46) << '\n';  
    // ok: 30680346300794274230660433647640397899788170645078853280082659754365153181696  
    cout << pow(boost::multiprecision::checked_int256_t(47), 47) << '\n';  
    // not ok: 56785318633014173688805680867629795740435407378067304340225355625363106944975  
    // should be: 3877924263464448622666648186154330754898344901344205917642325627886496\  
385062863  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-03-05 18:59:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/16#issuecomment-284251574  

Fixed in https://github.com/boostorg/multiprecision/commit/7a2df8ff0acd010ac0aa3defe0ef517a04e18920  
  
Thanks for the report!
