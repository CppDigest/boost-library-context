# #9 pow and reciprocal for rationals. [Closed]

> Username: mlang  
> Created at: 2015-09-05 12:05:14 UTC  
> Updated at: 2018-06-19 13:08:19 UTC  
> Closed at: 2018-06-18 01:18:02 UTC  
> Url: https://github.com/boostorg/rational/pull/9  

reciprocal() implements 1/x without a need for gcd. pow implements a decently  
efficient power function for rational base and integer exponent.  
  
Tests included.

---

## Comment 1

> Username: kundor  
> Created_at: 2016-05-14 03:02:05 UTC  
> Url: https://github.com/boostorg/rational/pull/9#discussion_r63270264  

It would be better to remove the `const&` from the parameter `base` and just use that, rather than copy it into `x` here.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2018-06-06 20:25:34 UTC  
> Url: https://github.com/boostorg/rational/pull/9#issuecomment-395202013  

AMDG  
  
On 06/06/2018 12:38 PM, James E. King III wrote:  
> jeking3 commented on this pull request.  
>   
> @kundor in that case we would just be moving the copy to the caller; it seems more correct (as it is currently stated) to claim that base will not be modified by the call.  
>   
  
Taking an argument by value is sometimes considered  
preferable because it allows the copy to be elided  
when the argument is a temporary.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: mlang  
> Created_at: 2018-06-19 13:08:19 UTC  
> Url: https://github.com/boostorg/rational/pull/9#issuecomment-398392498  

Thanks.

---
