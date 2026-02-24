# #27 Use throw() when noexcept is not available. [Closed]

> Username: uecasm  
> Created at: 2016-11-04 00:13:13 UTC  
> Updated at: 2016-11-07 08:21:15 UTC  
> Closed at: 2016-11-06 22:53:55 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/27  

I was wondering why `std::is_nothrow_move_assignable<boost::shared_ptr<T>>::value` was false, and this appears to be the reason.  
  
This is a simple mechanical replacement and hasn't been individually tested, but it seems reasonably safe. (`BOOST_NOEXCEPT_OR_NOTHROW` was added in https://github.com/boostorg/config/commit/50a562867e72af1796f42b905e34116b837b215e).  
  
Given http://www.boost.org/doc/libs/1_62_0/libs/smart_ptr/smart_ptr.htm#Exception-specifications, you might want to reject this, but I thought I'd suggest it just the same, as current behaviour is confusing in VS2013 (which supports most of C++11 and `throw()` but not `noexcept`).

---

## Comment 1

> Username: pdimov  
> Created_at: 2016-11-04 00:39:54 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/27#issuecomment-258315544  

Not going to sprinkle `throw()` all over smart_ptr, sorry. Even if we assume that the specific case of VS2013 warrants special treatment, of which I'm not convinced, the changes are excessive and unnecessary. Without the `noexcept` operator you can't query whether an arbitrary smart_ptr function throws.

---

## Comment 2

> Username: uecasm  
> Created_at: 2016-11-06 22:53:55 UTC  
> Updated_at: 2016-11-06 22:56:48 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/27#issuecomment-258717746  

Fair enough.  I did say I was expecting rejection, but thought it worth a shot anyway.  
  
(Though FWIW VS2013 does implement the `is_nothrow_x` traits and they do react to `throw()` as you'd expect, via compiler magic.  So the change I proposed would make those traits work as expected.)

---

## Comment 3

> Username: pdimov  
> Created_at: 2016-11-06 23:19:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/27#issuecomment-258719280  

Yes, I understood.  
  
If the traits reporting `true` under VS2013 is important for you, we could make the appropriate special functions `throw()` under VS2013.

---

## Comment 4

> Username: uecasm  
> Created_at: 2016-11-07 08:15:21 UTC  
> Updated_at: 2016-11-07 08:21:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/27#issuecomment-258771572  

It's not a big deal for me personally (just means some code goes down a less-optimal "snapshot in case of thrown exceptions" path), especially since I'm hoping to switch to VS2015 Real Soon Now™.  Just thought I'd raise it as a concern in case others were in a similar boat.  
  
But if you think it's worthwhile, I could narrow the PR to just the methods supported by the traits and reopen it.

---
