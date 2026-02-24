# #43 - emplace or/and boost::in_place [Open]

> Username: Kojoley  
> Created at: 2018-01-03 18:55:13 UTC  
> Updated at: 2018-11-11 11:10:16 UTC  
> Url: https://github.com/boostorg/variant/issues/43  

Is there a reason why `boost::variant` does not support in place object construction? The `std::variant` has `emplace<T>(...)` though.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-02-01 20:26:17 UTC  
> Url: https://github.com/boostorg/variant/issues/43#issuecomment-362391251  

There were no requests to implement it until now :)

---

## Comment 2

> Username: dpelle  
> Created at: 2018-11-11 11:10:16 UTC  
> Url: https://github.com/boostorg/variant/issues/43#issuecomment-437661041  

I would also like to have this.  I use ICU UnicodeString type in boost::variant, and UnicodeString is expensive to copy or move. So boost::variant::emplace(…) would help to improve performances.
