# #33 Fixes for msvc compilation [Closed]

> Username: jzmaddock  
> Created at: 2017-03-10 19:07:00 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2017-04-08 22:00:13 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/33  



---

## Comment 1

> Username: akrzemi1  
> Created_at: 2017-03-11 14:46:24 UTC  
> Updated_at: 2017-03-11 14:49:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/33#issuecomment-285871034  

Regarding the middle commit 104d739, is it not enough to wrap the function name in parentheses?:  
  
```c++  
(std::max)({log(Min), log(Max)})  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-03-11 17:56:13 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/33#issuecomment-285886117  

On 11/03/2017 14:46, Andrzej Krzemieński wrote:  
>  
> Regarding the middle commit 104d739   
> <https://github.com/robertramey/safe_numerics/commit/104d739d416a67970cfeed22cd74603b38bd8389>,   
> is it not enough to wrap the function name in parentheses?:  
>  
> (std::max)({log(Min),log(Max)})  
  
No, wrong fix, there are two issues here:  
  
* MSVC doesn't like the unified initialization syntax using ({a, b}) in   
this context.  Removing the { and } allows msvc to compile the file *as   
long as that variable is never instantiated*.  However, that fix happens   
to cause an internal compiler error in gcc-5.3 even though it's still   
legal C++.  
* If the variable is actually instantiated, then a second error occurs   
even with the above fix: an internal compiler error while instantiating   
std::max.  I haven't tracked down what it is about std::max that   
triggers the issue, but my simplified "max" fixes things.  
  
So yes, it's an ugly fix, but I couldn't find a simpler one.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 3

> Username: robertramey  
> Created_at: 2017-04-08 22:00:13 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/33#issuecomment-292748569  

a) substituted #include <boost/core/demangle.hpp>  
for gcc api   
b&c) no longer relevant due to other code changes

---
