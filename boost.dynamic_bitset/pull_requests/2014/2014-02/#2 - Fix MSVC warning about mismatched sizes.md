# #2 Fix MSVC warning about mismatched sizes. [Merged]

> Username: ahmedcharles  
> Created at: 2014-02-19 04:40:50 UTC  
> Updated at: 2014-02-19 06:49:48 UTC  
> Merged at: 2014-02-19 06:44:16 UTC  
> Closed at: 2014-02-19 06:44:16 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/2  



---

## Comment 1

> Username: mclow  
> Created_at: 2014-02-19 05:19:33 UTC  
> Updated_at: 2014-02-19 05:20:04 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/2#issuecomment-35467194  

A simple fix, and it certainly doesn't break anything on my system.  
  
But, but - the code there is somewhat obscure, using || for flow control.  
How about using something like this instead?  
  
```  
       streamsize npad = os.width() <= 0  ? 0 :  
                     (bitsetsize_type) os.width() <= b.size() ? 0 : os.width() - b.size();  
```  
  
or even:  
  
```  
       size_t npad = std::max(os.width(), 0);  
       if ( b.size() < npad )  
                  npad -= b.size();  
```  
  
Along the lines of "leaving the code cleaner than when you found it".

---

## Comment 2

> Username: ahmedcharles  
> Created_at: 2014-02-19 05:26:28 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/2#issuecomment-35467492  

I didn't even notice that the expression was that complex. I'll take another look.

---

## Comment 3

> Username: ahmedcharles  
> Created_at: 2014-02-19 06:06:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/2#issuecomment-35469117  

This should be easier to understand.

---

## Comment 4

> Username: mclow  
> Created_at: 2014-02-19 06:33:51 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/2#issuecomment-35470411  

I like that much better. Thanks!  
Do you want me to merge it, or will you?

---

## Comment 5

> Username: ahmedcharles  
> Created_at: 2014-02-19 06:42:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/2#issuecomment-35470813  

I should probably create a merge point between develop and master first, just realized that there isn't one.

---

## Comment 6

> Username: mclow  
> Created_at: 2014-02-19 06:42:58 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/2#issuecomment-35470842  

I'd appreciate that.

---

## Comment 7

> Username: ahmedcharles  
> Created_at: 2014-02-19 06:43:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/2#issuecomment-35470890  

I also can't merge it since I don't have permissions.

---
