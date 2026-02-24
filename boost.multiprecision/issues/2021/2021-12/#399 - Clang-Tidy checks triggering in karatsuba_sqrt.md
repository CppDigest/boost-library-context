# #399 - Clang-Tidy checks triggering in karatsuba_sqrt [Closed]

> Username: johnmcfarlane  
> Created at: 2021-12-06 16:23:01 UTC  
> Updated at: 2021-12-10 20:45:27 UTC  
> Closed at: 2021-12-09 09:24:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/399  

Hi. I just ran Clang-Tidy on my project which uses multiprecision via Boost 1.77 and I see a check failing in latest _integer.hpp_.  
  
```  
blah/include/boost/multiprecision/integer.hpp:257:6: error: Assigned value is garbage or undefined [clang-analyzer-core.uninitialized.Assign,-warnings-as-errors]  
   s <<= (b - 1); // we already <<1 it before  
     ^  
```  
  
Here's [the run](https://github.com/johnmcfarlane/cnl/actions/runs/1545459663). (For comparison, here's [the same pipeline only with 1.76](https://github.com/johnmcfarlane/cnl/actions/runs/1545462718). It has other issues but not with MP.)  
  
I have not investigated yet but thought it might be of interest in the case that it's not a false positive, is a problem on your side, and is easy to fix. If you suspect that it's legit but cannot easily repro, please LMK and I'll try to reduce my case. Thanks.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-12-08 19:31:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/399#issuecomment-989131239  

I don't really see what's being complained about here: both variables s and b are initialized at point of declaration, and their values are such that overflow cannot occur (rationale: given B bits in their representation we have b = B/4 and s < 2^(B/2)).

---

## Comment 2

> Username: johnmcfarlane  
> Created at: 2021-12-09 09:24:28 UTC  
> Updated at: 2021-12-10 20:45:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/399#issuecomment-989664493  

I agree I don't think there's a bug. This seems only to occur in released versions of LLVM, not HEAD. But it's hard to silence this warning from user code without suppressing the entire check or pinning boost to 1.76.  
  
For future reference, the repro is:  
  
```c++  
#include <boost/multiprecision/integer.hpp>  
  
int main()  
{  
    auto y = sqrt(boost::multiprecision::int128_t{25});  
    return static_cast<int>(y);  
}  
```  
  
And the command (with LLVM 12) is:  
  
```bash  
clang-tidy source.cpp -- /usr/bin/clang++ -isystem /path/to/boost/1.77.0/include -c source.cpp  
```
