# #237 [strategies][cartesian][buffer][end_round] fix internal point count [Merged]

> Username: mkaravel  
> Created at: 2015-03-02 19:13:22 UTC  
> Updated at: 2015-03-02 21:17:16 UTC  
> Merged at: 2015-03-02 20:44:24 UTC  
> Closed at: 2015-03-02 20:44:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/237  

for small input values (less than 4): when the input point count is less  
than 4, set the internal point count to 4; for values less than 4 the  
generated ends do not look round;

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-02 20:44:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/237#issuecomment-76816214  

Thnks

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-03-02 20:45:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/237#issuecomment-76816722  

BTW it is OK for now, for next release I'm considering throwing exceptions if the number of points is not enough, or too much, for join strategies (and probably for miter factor or other relevant values)

---
