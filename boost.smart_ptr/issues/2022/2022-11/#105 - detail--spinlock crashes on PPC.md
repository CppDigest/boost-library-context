# #105 - detail::spinlock crashes on PPC [Closed]

> Username: pdimov  
> Created at: 2022-11-10 13:23:25 UTC  
> Updated at: 2022-11-21 09:53:22 UTC  
> Closed at: 2022-11-12 12:59:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/105  

https://github.com/PurpleI2P/i2pd/issues/1726  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=107590  
  
Apparently, `__atomic_test_and_set` requires an operand with the alignment of `bool`, and does not necessarily support `char`, despite what the documentation says; and `bool` is 32 bit on PPC.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-12 12:59:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/105#issuecomment-1312473820  

Fixed on develop.

---

## Comment 2

> Username: barracuda156  
> Created at: 2022-11-21 06:16:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/105#issuecomment-1321512599  

@pdimov Is it safe to backport?

---

## Comment 3

> Username: pdimov  
> Created at: 2022-11-21 09:53:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/105#issuecomment-1321788000  

It should be.
