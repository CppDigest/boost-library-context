# #732 Fix typo in #include composite.hpp [Merged]

> Username: georgthegreat  
> Created at: 2022-08-30 11:44:14 UTC  
> Updated at: 2022-09-03 13:19:44 UTC  
> Merged at: 2022-09-03 13:19:33 UTC  
> Closed at: 2022-09-03 13:19:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/732  



---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2022-08-30 13:48:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/732#pullrequestreview-1090359289  

📁 include/boost/spirit/home/classic/utility/scoped_lock.hpp

```diff
  12 | #include <boost/spirit/home/classic/namespace.hpp>
  13 | #include <boost/thread/lock_types.hpp>
  14 | #if !defined(BOOST_SPIRIT_COMPOSITE_HPP)
```

> Username: Kojoley  
> Created_at: 2022-08-30 13:48:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/732#discussion_r958507377  

@djowel do you have any idea why there is that `#if`?  It predates migration from CVS, as well as the include guard in the included file.

> Username: djowel  
> Created_at: 2022-08-30 23:46:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/732#discussion_r959021125  

I have no idea, or perhaps I already forgot.

> Username: Kojoley  
> Created_at: 2022-08-31 00:31:34 UTC  
> Updated_at: 2022-08-31 00:31:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/732#discussion_r959039752  

I am going to make the include unconditional then, cannot imagine why it would be wrong.


---

## Comment 2

> Username: georgthegreat  
> Created_at: 2022-08-30 13:52:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/732#issuecomment-1231700842  

NB: we have found this typo by using the automated dependency parser for C++.  
Not sure if this #include ever works

---

## Comment 3

> Username: Kojoley  
> Created_at: 2022-08-30 14:01:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/732#issuecomment-1231712328  

> Not sure if this #include ever works  
  
It could be hit by directly including the header which is explicitly allowed (https://www.boost.org/doc/libs/1_79_0/libs/spirit/classic/doc/includes.html), though it seems no one did that or reported the issue.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2022-09-03 13:19:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/732#issuecomment-1236119290  

Thanks for the catch!

---
