# #61 :new: Add `mp_quote_expr` to quote with lambda expressions [Open]

> Username: kris-jusiak  
> Created at: 2021-06-12 18:01:52 UTC  
> Updated at: 2021-08-29 11:20:30 UTC  
> Url: https://github.com/boostorg/mp11/pull/61  

Problem:  
- There is no easy way to quote metafunction directly with lambda expressions.  
  
Solution:  
- Add mp_quote_expr.  
  
Note:  
- mp_quote_expr requires C++20.

---

## Comment 1

> Username: kris-jusiak  
> Created_at: 2021-06-16 16:38:18 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-862532803  

@pdimov would you be able to take a look, please?

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-06-16 16:45:48 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-862540424  

Do you have an example of actual use? The example you're adding to the docs doesn't quite do what it says in the description; it replaces all types not having `value` with `void`, instead of finding those that have `value`.  
  
You need to check feature macros against their specified values (e.g. `>= 2019xx`), not just whether they are defined. (For a lambda to be usable as `Expr` the compiler also needs to support lambdas in unevaluated contexts, but I suppose the trait doesn't technically require that.)

---

## Comment 3

> Username: kris-jusiak  
> Created_at: 2021-06-16 16:51:18 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-862544989  

Thanks for your feedback, @pdimov.  
  
Sure, I'll update the example to more descriptive. The basic idea is just to enable C++20 style lambdas (especially with if constexpr and requires) for simplicity of usage (instead of declaring aliases).  
  
Yeah, I was trying that but both clang and gcc didn't set the date correctly.

---

## Comment 4

> Username: kris-jusiak  
> Created_at: 2021-06-16 23:08:26 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-862789102  

@pdimov Updated the example. Hopefully, the update makes it more clear/descriptive.  
I also check again whether feature macros can be used purely without any workarounds but it seems that they always return the same standard date although the implementation differs :/  
* https://godbolt.org/z/Ga9zx4Gs8

---

## Comment 5

> Username: kris-jusiak  
> Created_at: 2021-06-19 17:03:12 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-864436747  

@pdimov I updated feature macros to verify the date as you suggested. I hope that works for you. Is there anything else you would like me to address? Thank you.

---

## Comment 6

> Username: pdimov  
> Created_at: 2021-06-19 22:50:54 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-864473057  

It's fine; but I have more pressing things to do at the moment, so I'll need to come back to this later, and maybe not in time for 1.77.

---

## Comment 7

> Username: kris-jusiak  
> Created_at: 2021-07-20 20:07:46 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-883661236  

No worries @pdimov , fully understood. Thank you. Pinging, just in case you are free and/or to show that the MR isn't stale.

---

## Comment 8

> Username: kris-jusiak  
> Created_at: 2021-08-20 13:14:47 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-902684854  

Hey @pdimov. I know you are busy, just pinging in case you are available, no worries if not. Thanks.

---

## Comment 9

> Username: pdimov  
> Created_at: 2021-08-29 11:20:30 UTC  
> Url: https://github.com/boostorg/mp11/pull/61#issuecomment-907774911  

I sat on this for a while because I wasn't sure what I want to do with it. Adding references doesn't feel like the right thing to do, for example.  
  
But I've now made up my mind and I don't think it belongs in the library, it's not a good fit conceptually. Sorry.  
  
It's still cool and would make a good blog post.

---
