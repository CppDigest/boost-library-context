# #2 Add constexpr usage checks [Merged]

> Username: jzmaddock  
> Created at: 2014-07-28 10:32:02 UTC  
> Updated at: 2015-04-02 02:24:13 UTC  
> Merged at: 2015-04-02 02:24:12 UTC  
> Closed at: 2015-04-02 02:24:12 UTC  
> Url: https://github.com/boostorg/rational/pull/2  

Add constexpr usage checks - without these we can't be sure that other changes don't break something.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-03-30 15:44:59 UTC  
> Url: https://github.com/boostorg/rational/pull/2#issuecomment-87728314  

LGTM.

---

## Comment 2

> Username: pdimov  
> Created_at: 2015-03-30 16:09:34 UTC  
> Url: https://github.com/boostorg/rational/pull/2#issuecomment-87737332  

Maybe put those in a separate test?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-03-30 17:01:43 UTC  
> Url: https://github.com/boostorg/rational/pull/2#issuecomment-87751564  

> Maybe put those in a separate test?  
>   
> I could do, but it's probably overkill just for those few tests?

---

## Comment 4

> Username: pdimov  
> Created_at: 2015-03-30 17:10:47 UTC  
> Url: https://github.com/boostorg/rational/pull/2#issuecomment-87753941  

I think it's justified - these tests are about a specific feature, constexpr support, which is independent of the rest. But it's a matter of taste, really.

---

## Comment 5

> Username: ahmedcharles  
> Created_at: 2015-03-31 02:27:54 UTC  
> Url: https://github.com/boostorg/rational/pull/2#issuecomment-87903912  

I agree with @pdimov, but otherwise looks fine.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-03-31 11:14:16 UTC  
> Url: https://github.com/boostorg/rational/pull/2#issuecomment-88048118  

constexpr checks now moved to a separate file.

---
