# #298 Cubic Hermite spline interpolation. [Merged]

> Username: NAThompson  
> Created at: 2020-01-10 18:41:41 UTC  
> Updated at: 2020-01-23 02:29:24 UTC  
> Merged at: 2020-01-19 14:48:24 UTC  
> Closed at: 2020-01-19 14:48:24 UTC  
> Url: https://github.com/boostorg/math/pull/298  

This should backend both the pchip and makima spline.

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2020-01-20 22:54:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/298#pullrequestreview-345563166  

📁 include/boost/math/interpolators/cubic_hermite.hpp

```diff
  29 |+     }
  30 |+ 
  31 |+     friend std::ostream& operator<<(std::ostream & os, const cubic_hermite & m)
```

> Username: jeremy-murphy  
> Created_at: 2020-01-20 22:54:16 UTC  
> Url: https://github.com/boostorg/math/pull/298#discussion_r368750621  

I notice that `std::ostream` is used here but no i/o header is included above?  
As a side note, do we not typically put i/o functions in separate headers so that users can avoid including them altogether if they wish?

> Username: NAThompson  
> Created_at: 2020-01-20 23:20:06 UTC  
> Url: https://github.com/boostorg/math/pull/298#discussion_r368755467  

It's in the `boost/math/interpolators/detail/cubic_hermite_detail.hpp`.

> Username: NAThompson  
> Created_at: 2020-01-20 23:21:45 UTC  
> Url: https://github.com/boostorg/math/pull/298#discussion_r368755779  

I didn't know of any such rule; how helpful would it be? We'd lose helpful debug info.

> Username: jeremy-murphy  
> Created_at: 2020-01-21 07:28:08 UTC  
> Updated_at: 2020-01-21 07:28:09 UTC  
> Url: https://github.com/boostorg/math/pull/298#discussion_r368844363  

> It's in the `boost/math/interpolators/detail/cubic_hermite_detail.hpp`.  
  
Some people consider it a pedantic physical design rule, but the point is about dependency: this header now depends on the detail header to include a standard header for it. And for someone editing the detail header, they couldn't know how many other headers are depending on it to do that. So the rule is: don't depend on other headers to include things for you.  :)

> Username: jeremy-murphy  
> Created_at: 2020-01-21 07:31:57 UTC  
> Url: https://github.com/boostorg/math/pull/298#discussion_r368845529  

> I didn't know of any such rule; how helpful would it be? We'd lose helpful debug info.  
  
It's not a rule, not a written one anyway, just something I have seen done elsewhere. Boost.Tuple, I think, and that's kinda old now, so maybe this idea doesn't apply anymore.  
The point is not to lose anything, but just to make it optional, so that people who don't need it don't have to compile it.  
I'm not sure what `std::ostream` brings in, maybe it's just an interface, but for example on many embedded targets, including `<iostream>` is strictly forbidden because of the extra ROM space it takes up. Some people just want to compute, and they don't care about the debugging.


---

## Comment 2

> Username: pabristow  
> Created_at: 2020-01-21 12:17:13 UTC  
> Url: https://github.com/boostorg/math/pull/298#issuecomment-576655985  

You are right that there are some users who want to avoid iostream, but it generates significantly more complexity and clutter to enclose all uses with some macro like 'ifdef BOOST_MATH_CUBIC_HERMITE_NO_IOSTREAM to control use, or not.  And it will have a small effect on compile time.  Retrofitting is a daunting (and dispiriting) task.  So far, we have generally valued the diagnostic info enough, especially while grappling with the algorithms themselves, to default to assuming iostream, and lots of parameter and other checks where possible.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2020-01-23 02:29:24 UTC  
> Url: https://github.com/boostorg/math/pull/298#issuecomment-577476867  

Physical separation (as per Boost.Tuple) is a lot simpler than conditional separation via a macro.  
But if it's not important right now, don't worry.

---
