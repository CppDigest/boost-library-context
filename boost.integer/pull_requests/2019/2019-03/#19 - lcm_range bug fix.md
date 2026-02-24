# #19 lcm_range bug fix [Closed]

> Username: sgibeau  
> Created at: 2019-03-25 18:42:48 UTC  
> Updated at: 2019-03-25 23:00:31 UTC  
> Closed at: 2019-03-25 23:00:31 UTC  
> Url: https://github.com/boostorg/integer/pull/19  

Iteration should not stop when encountering an element with a value of 1.  
lcm_range of 1,4 should return 4.

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-03-25 22:32:22 UTC  
> Url: https://github.com/boostorg/integer/pull/19#issuecomment-476403041  

I think, that should be comparison against 0 instead. Also, we might need a test for that case.

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-03-25 23:00:31 UTC  
> Url: https://github.com/boostorg/integer/pull/19#issuecomment-476409797  

Should be fixed in 33c1655, thanks.

---
