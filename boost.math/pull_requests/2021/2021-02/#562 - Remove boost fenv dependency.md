# #562 Remove boost fenv dependency [Merged]

> Username: mborland  
> Created at: 2021-02-27 18:22:59 UTC  
> Updated at: 2021-03-20 12:27:26 UTC  
> Merged at: 2021-02-28 14:32:16 UTC  
> Closed at: 2021-02-28 14:32:16 UTC  
> Url: https://github.com/boostorg/math/pull/562  

I suspect a lot more of this can be pared back, but lack the knowledge and numerous platforms to test everything.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-02-27 18:53:42 UTC  
> Url: https://github.com/boostorg/math/pull/562#issuecomment-787118728  

That all looks fine too.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-28 13:23:33 UTC  
> Url: https://github.com/boostorg/math/pull/562#issuecomment-787451641  

@jzmaddock this is good to go.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-02-28 14:32:00 UTC  
> Url: https://github.com/boostorg/math/pull/562#issuecomment-787461801  

It also appears that `#define BOOST_MATH_IS_FLOAT(T) (std::is_floating_point<T>::value)` is superfluous now. Doesn't need to be done in the PR of course.

---
