# #18 fix negation in dynamic_bitset [Closed]

> Username: olk  
> Created at: 2018-01-29 09:58:09 UTC  
> Updated at: 2018-01-31 06:34:21 UTC  
> Closed at: 2018-01-31 06:33:58 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/18  



---

## Comment 1

> Username: mclow  
> Created_at: 2018-01-29 15:06:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/18#issuecomment-361273809  

Um... what does this fix?  [I'm missing the context for this change]

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-01-29 15:16:17 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/18#issuecomment-361277030  

The fix is that negation should be on 0 as an integer, not on 0 as a Block. This fix looks correct to me.

---

## Comment 3

> Username: mclow  
> Created_at: 2018-01-29 15:35:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/18#issuecomment-361283298  

> The fix is that negation should be on 0 as an integer, not on 0 as a Block.  
  
Yes, I see that's what the code is doing.  
But that's not what I was asking.

---

## Comment 4

> Username: olk  
> Created_at: 2018-01-30 07:59:29 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/18#issuecomment-361506118  

compiling unit-tests with _gcc -W -Wall -Werror_ produces errors like:  
  
../../../boost/dynamic_bitset/dynamic_bitset.hpp:1953:57: error: left shift of negative value [-Werror=shift-negative-value]  
         block_type const mask = (~static_cast<Block>(0) << extra_bits);

---

## Review 5 [Commented]

> Username: mclow  
> Created_at: 2018-01-30 21:40:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/18#pullrequestreview-92731362  

📁 include/boost/dynamic_bitset/dynamic_bitset.hpp

```diff
1072 |         }
1073 |-         block_type const mask = ~(~static_cast<Block>(0) << extra_bits);
1073 |+         block_type const mask = ~(static_cast<Block>(~0) << extra_bits);
```

> Username: mclow  
> Created_at: 2018-01-30 21:40:17 UTC  
> Updated_at: 2018-01-30 21:40:32 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/18#discussion_r164886920  

So much strangeness here.  You're making a value, casting it to a `Block`, and then assigning it to a `block_type`. If they were different types, you'd be doing a conversion. But they're not - so why use two different names?  
Also, we know that `block_type` is an unsigned integral type, right? So why start with a signed constant `0`?  
Why not just write:  
     `block_type const mask = (block_type(1) << extra_bits) - 1;`  
Isn't that the same value? No more `~`s, no `static_cast`.


---

## Comment 6

> Username: olk  
> Created_at: 2018-01-31 06:34:21 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/18#issuecomment-361837909  

I've added a bug report

---
