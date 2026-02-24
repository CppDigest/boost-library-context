# #614 Remove leading zeros from base-10 numbers [Closed]

> Username: mborland  
> Created at: 2024-04-16 08:02:08 UTC  
> Updated at: 2025-07-01 17:35:12 UTC  
> Closed at: 2024-04-16 12:42:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/614  

Currently we assume that anything with a leading 0 is in octal format. Change to where octal has to be 0 followed by a non-zero number. If the format does not meet that requirement for octal than remove any and all leading zeros.  
  
Closes: #612

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-04-16 09:02:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/614#issuecomment-2058595906  

> Change to where octal has to be 0 followed by a non-zero number.  
  
Ah now I get it.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-04-16 09:43:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/614#issuecomment-2058677120  

@jzmaddock Any issue with this change? It would technically be a breaking change, but I think the new behavior is less surprising.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-04-16 12:16:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/614#issuecomment-2058950007  

> Any issue with this change? It would technically be a breaking change, but I think the new behavior is less surprising.  
  
I do worry about this, consider a block of formatted numbers:  
  
```  
0001  
0002  
0003  
...  
0099  
0100  
```  
  
We start off as decimal, and then there is a step change to octal when we get to "0100" which seems even worse to me?

---

## Comment 4

> Username: mborland  
> Created_at: 2024-04-16 12:42:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/614#issuecomment-2059000955  

Godbolt says leading zeros is still octal: https://godbolt.org/z/7Kcahjh67. Since the original ticket is closed I'll just close this too.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-04-16 17:33:32 UTC  
> Updated_at: 2024-04-16 17:33:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/614#issuecomment-2059595704  

> start off as decimal, and then there is a step change to octal   
  
Oh geez I actually had that one last century. It took 3 days to resolve. But now that I reflecrt (which I had entirely forgotten) so is the spec.

---
