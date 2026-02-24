# #2803 Fix unreachable code warning in `buffers_cat.hpp` [Merged]

> Username: ashtum  
> Created at: 2024-01-19 11:27:40 UTC  
> Updated at: 2024-01-22 16:25:56 UTC  
> Merged at: 2024-01-22 16:25:56 UTC  
> Closed at: 2024-01-22 16:25:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2803  

It seems we can't simply assume that the code for `past_end::operator*()` will never compile, as the following code clearly needs to handle that case:  
```C++  
return mp11::mp_with_index<  
    sizeof...(Bn) + 2>(  
        it_.index(), // Considering the index is a runtime value, the compiler needs to generate code for when it points to `past_end`  
        dereference{*this});  
```  
  
Fixes #2678

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-19 12:26:24 UTC  
> Updated_at: 2024-01-19 12:47:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2803#issuecomment-1900326650  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2803?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`6adca47`)](https://app.codecov.io/gh/boostorg/beast/commit/6adca47bbe7acd854070fd852e84f280a14a892a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.01% compared to head [(`9c773e7`)](https://app.codecov.io/gh/boostorg/beast/pull/2803?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.04%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2803/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2803?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2803      +/-   ##  
===========================================  
+ Coverage    93.01%   93.04%   +0.02%       
===========================================  
  Files          178      178                
  Lines        13688    13688                
===========================================  
+ Hits         12732    12736       +4       
+ Misses         956      952       -4       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2803?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/buffers\_cat.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2803?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19jYXQuaHBw) | `98.42% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2803/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2803?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2803?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6adca47...9c773e7](https://app.codecov.io/gh/boostorg/beast/pull/2803?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2024-01-20 21:43:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2803#issuecomment-1902272024  

Interesting, is this why we keep getting that damn warning !?

---

## Comment 3

> Username: ashtum  
> Created_at: 2024-01-21 05:58:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2803#issuecomment-1902524870  

> Interesting, is this why we keep getting that damn warning !?  
  
I believe so. The reason we are getting those warnings is that the returned `net::mutable_buffer` from the `past_end::operator*` function (which is marked unreachable) implicitly converts to `net::const_buffer`. For example, the following generates the warning:  
https://godbolt.org/z/83G5f4Wx7  
```C++  
auto buf = buffers_cat(net::const_buffer{}, net::const_buffer{});  
auto it  = buf.begin();  
*it;  
```  
However, this doesn't:  
https://godbolt.org/z/WePad646W  
```C++  
auto buf = buffers_cat(net::mutable_buffer{}, net::mutable_buffer{});  
auto it  = buf.begin();  
*it;  
```  
  
This is quite comparable to this example:   
https://godbolt.org/z/8fMroWh54  
```C++  
[[noreturn]] const char * f1()  
{  
  __assume(false);  
}  
  
const char * f2()  
{  
  return f1(); // Fine  
}  
  
std::string f3()  
{  
  return f1(); // warning C4702: unreachable code  
}  
```   
  
MSVC is correctly warning us that we are doing something after unreachable code (in this case, the implicit conversion is what we are doing).

---
