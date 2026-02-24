# #413 Making overload_t's copy constructor have priority [Open]

> Username: DarthRubik  
> Created at: 2018-07-01 01:31:03 UTC  
> Updated at: 2021-03-31 11:03:05 UTC  
> Url: https://github.com/boostorg/hana/pull/413  

So currently the copy constructor for the overload_t does not take  
precendent over the other templated constructor.  This means a simple  
function like the following fails:  
  
	void foo()  
	{  
		auto a = hana::overload(f1,f2);  
		auto b(a); //Error  
	}  
  
To fix this I added a enable_if to turn off the constructor when not  
applicable.........  
  
Note: This (allegedly) fixes issue 412

---

## Comment 1

> Username: DarthRubik  
> Created_at: 2018-07-01 03:17:26 UTC  
> Url: https://github.com/boostorg/hana/pull/413#issuecomment-401580208  

I am not quite sure why the automated builds failed......(I don't have much experience with appveyor or travis-ci).....when I did a local make check nothing failed........does someone have a clue as to what is going on here?

---

## Comment 2

> Username: ricejasonf  
> Created_at: 2018-07-01 04:28:29 UTC  
> Updated_at: 2018-07-01 04:29:14 UTC  
> Url: https://github.com/boostorg/hana/pull/413#issuecomment-401582214  

It looks like it is detecting trailing white-space. I remember the error message for that being a little better. (err it actually had an error message for that at one time)  
  
The Appveyor build failure looks like a config issue with MSVC.

---

## Review 3 [Changes requested]

> Username: ldionne  
> Created_at: 2018-07-18 15:40:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/hana/pull/413#pullrequestreview-138314280  

Looks good to me, with cosmetic changes. Also, please squash the two commits into one to avoid noise in the history.

📁 test/issues/github_412.cpp

```diff
   1 |+ #include "boost/hana.hpp"
```

> Username: ldionne  
> Created_at: 2018-07-18 15:36:01 UTC  
> Updated_at: 2018-07-18 15:40:14 UTC  
> Url: https://github.com/boostorg/hana/pull/413#discussion_r203427359  

We use `<>` to include Hana's headers in the tests.  Also, we try to include the most specific header. `#include <boost/hana/functional/overload.hpp>`


📁 include/boost/hana/functional/overload.hpp

```diff
  51 |+         template <typename F_, typename ...G_,
  52 |+                   typename = std::enable_if_t<
  53 |+                      !std::is_same<overload_t,std::decay_t<F_>>::value
```

> Username: ldionne  
> Created_at: 2018-07-18 15:39:25 UTC  
> Updated_at: 2018-07-18 15:40:14 UTC  
> Url: https://github.com/boostorg/hana/pull/413#discussion_r203428649  

Nitpick: Please add a space between `overload_t` and `std::decay_t<F_>`.


---
