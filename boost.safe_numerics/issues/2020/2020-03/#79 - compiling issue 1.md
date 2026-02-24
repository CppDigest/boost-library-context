# #79 - compiling issue 1 [Closed]

> Username: GregKon  
> Created at: 2020-03-10 19:36:01 UTC  
> Updated at: 2020-11-28 17:00:22 UTC  
> Closed at: 2020-03-11 03:17:13 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/79  

Dear Robert  
  
I have compiling issue on my compiler:  
  
`    const union {  
        R m_r;  
        char const * m_msg;  
    }`  
in checked_results.hpp  
  
In general const before union make warning: "type qualifiers are meaningless in this declaration" but later on compiling error. Remove const work fine.  
From my knowledge it not make sense, declaration with "const" without instantiation.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-03-11 03:15:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/79#issuecomment-597422897  

I agree with IAR in this case.  I'm removing the "const"

---

## Comment 2

> Username: GregKon  
> Created at: 2020-11-28 17:00:22 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/79#issuecomment-735256229  

Dear Robert  
  
I have just merge from master, this patch is still not there.  
It does not break anything, tested on GCC and MSVC.
