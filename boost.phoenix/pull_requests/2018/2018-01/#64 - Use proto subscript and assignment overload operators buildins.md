# #64 Use proto subscript and assignment overload operators buildins [Merged]

> Username: Kojoley  
> Created at: 2018-01-26 18:46:37 UTC  
> Updated at: 2018-02-01 09:50:08 UTC  
> Merged at: 2018-02-01 01:00:00 UTC  
> Closed at: 2018-02-01 01:00:00 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64  

Fixes #62.

---

## Comment 1

> Username: djowel  
> Created_at: 2018-01-26 21:11:13 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64#issuecomment-360905326  

Oh no... IIRC there's a reason why we have those instead of the builtins. I have to jog my memory though. @sithhell , do you remember?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-01-26 21:33:22 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64#issuecomment-360910166  

I was also feeling it is here for a reason, but failed to find one, then I gave it a try and it works, also fixing an issue...

---

## Comment 3

> Username: djowel  
> Created_at: 2018-01-26 21:34:51 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64#issuecomment-360910492  

> I was also feeling it is here for a reason, but failed to find one, then I gave it a try and it works, also fixing an issue...  
  
I may be wrong. It's been a while since I looked at the code.

---

## Review 4 [Commented]

> Username: Flast  
> Created_at: 2018-01-27 03:52:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/phoenix/pull/64#pullrequestreview-92014526  

📁 include/boost/phoenix/core/actor.hpp

```diff
 208 |-         actor& operator=(actor const& other)
 209 |-         {
 210 |-             detail::assign()(*this, other);
```

> Username: Flast  
> Created_at: 2018-01-27 02:51:11 UTC  
> Updated_at: 2018-01-27 12:14:41 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64#discussion_r164261158  

Can't definition of `detail::assign` be removed?

> Username: Kojoley  
> Created_at: 2018-01-27 12:14:15 UTC  
> Updated_at: 2018-01-27 12:14:41 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64#discussion_r164270641  

Yeah, it can. Copy ctor was the last thing I have removed and missed that fact.


---

## Comment 5

> Username: Kojoley  
> Created_at: 2018-01-27 12:18:08 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64#issuecomment-360981107  

With this patch `actor` definition is closer to what one can find in [proto's lambda example](http://www.boost.org/doc/libs/master/doc/html/proto/users_guide.html#boost_proto.users_guide.examples.lambda).

---

## Review 6 [Approved]

> Username: Flast  
> Created_at: 2018-01-31 02:58:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/phoenix/pull/64#pullrequestreview-92794499  

LGTM, @djowel @sithhell anything else?

---

## Comment 7

> Username: djowel  
> Created_at: 2018-01-31 04:59:38 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64#issuecomment-361824281  

I'm good with it as long as all the tests pass. The concern might be no longer relevant.

---

## Comment 8

> Username: Flast  
> Created_at: 2018-02-01 00:59:54 UTC  
> Url: https://github.com/boostorg/phoenix/pull/64#issuecomment-362123134  

OK

---
