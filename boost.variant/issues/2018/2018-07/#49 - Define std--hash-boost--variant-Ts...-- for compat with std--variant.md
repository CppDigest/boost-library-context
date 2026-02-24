# #49 - Define std::hash<boost::variant<Ts...>> for compat with std::variant [Closed]

> Username: alexweej  
> Created at: 2018-07-06 10:54:14 UTC  
> Updated at: 2019-04-12 07:56:37 UTC  
> Closed at: 2019-04-12 07:56:37 UTC  
> Url: https://github.com/boostorg/variant/issues/49  

As in https://github.com/boostorg/optional/issues/55 for optional, it would be useful to provide this so we can use C++11 unordered_map with boost::variant and have a similar experience to C++17 std::variant.  
  
@akrzemi1

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-08-12 18:02:45 UTC  
> Url: https://github.com/boostorg/variant/issues/49#issuecomment-412360619  

This is a tempting idea however it has many complications:  
* libc++ defines `hash` in namespace `std::__1::` rather than in namespace `std::`  
* VC defined `class hash`, rather than `struct hash`.  
* ???  
  
@akrzemi1 have you dealt with those issues? Probably we need a common solution here (some macro in Boost.Config to specialize `std::hash`?)

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-08-17 08:50:28 UTC  
> Url: https://github.com/boostorg/variant/issues/49#issuecomment-413800973  

No. Did not address it yet in `boost::optional`. However, in this implementation of Optional: https://github.com/akrzemi1/Optional/ I have simply added a specialization of `std::hash` and never heard any complaints about it. Maybe nobody uses it. Or maybe the problems you describe apply only to pre-c++11 compilers?

---

## Comment 3

> Username: apolukhin  
> Created at: 2018-08-28 09:11:13 UTC  
> Url: https://github.com/boostorg/variant/issues/49#issuecomment-416510010  

Probably all my problems are because of the forward declaration of `std::hash`. I'll try without is soon.

---

## Comment 4

> Username: apolukhin  
> Created at: 2019-04-02 15:36:35 UTC  
> Url: https://github.com/boostorg/variant/issues/49#issuecomment-479055651  

Looks like 31dcc43 does the job. I'll merge it after the 1.70 release

---

## Comment 5

> Username: apolukhin  
> Created at: 2019-04-12 07:56:37 UTC  
> Url: https://github.com/boostorg/variant/issues/49#issuecomment-482477468  

Fixed in #65
