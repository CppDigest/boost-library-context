# #19 Update string_ref_test2.cpp [Merged]

> Username: akumta  
> Created at: 2015-02-25 18:53:07 UTC  
> Updated at: 2015-02-27 17:36:14 UTC  
> Merged at: 2015-02-27 06:26:11 UTC  
> Closed at: 2015-02-27 06:26:11 UTC  
> Url: https://github.com/boostorg/utility/pull/19  

For ticket# 10838

---

## Comment 1

> Username: mclow  
> Created_at: 2015-02-25 19:22:04 UTC  
> Url: https://github.com/boostorg/utility/pull/19#issuecomment-76035278  

Can you tell me what compiler/library combination you're using?  
[ Since `#include <cstring>` _should_ get you `::strlen` ]  
  
P.S.  I'm not opposed to making this change, btw.

---

## Comment 2

> Username: akumta  
> Created_at: 2015-02-25 19:41:36 UTC  
> Url: https://github.com/boostorg/utility/pull/19#issuecomment-76039479  

I am using Oracle Solaris Studio 12.4 compiler,  and compile with   
-library=stlport4.  
  
On 02/25/15 11:22, Marshall Clow wrote:  
  
> Can you tell me what compiler/library combination you're using?  
> [ Since |#include <cstring>| /should/ get you |::strlen| ]  
>   
> P.S. I'm not opposed to making this change, btw.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/utility/pull/19#issuecomment-76035278.

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-02-26 03:45:48 UTC  
> Updated_at: 2015-02-26 03:46:28 UTC  
> Url: https://github.com/boostorg/utility/pull/19#issuecomment-76117545  

@mclow No, cstring only provides std::strlen. ::strlen is provided by string.h. The fact that c... headers are implemented by a pack of using declarations is a common implementation detail.

---

## Comment 4

> Username: akumta  
> Created_at: 2015-02-27 17:36:14 UTC  
> Url: https://github.com/boostorg/utility/pull/19#issuecomment-76437248  

Thank you.  
  
On 02/26/15 22:26, Andrey Semashev wrote:  
  
> Merged #19 https://github.com/boostorg/utility/pull/19.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/utility/pull/19#event-243080895.

---
