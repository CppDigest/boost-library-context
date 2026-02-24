# #12 Added base_from_member specialization for members of lvalue-reference ty... [Merged]

> Username: K-ballo  
> Created at: 2014-06-11 22:00:40 UTC  
> Updated at: 2014-06-12 19:47:07 UTC  
> Merged at: 2014-06-11 23:54:08 UTC  
> Closed at: 2014-06-11 23:54:08 UTC  
> Url: https://github.com/boostorg/utility/pull/12  

[ see https://svn.boost.org/trac/boost/ticket/7577 for context ]  
  
Thoughts? The partial specialization is not needed in C++11 mode, but it seems cleaner to provide it just as well.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-06-11 22:30:43 UTC  
> Url: https://github.com/boostorg/utility/pull/12#issuecomment-45808803  

Looks valid to me, although I was surprised that the general specialization didn't attempt perfect forwarding (or the next best thing in C++03) and passed arguments by value instead. Maybe perfect forwarding would be a better solution?

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-06-11 22:35:08 UTC  
> Url: https://github.com/boostorg/utility/pull/12#issuecomment-45809194  

Oh, I can see the C++11 version actually does perfect forwarding.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-06-11 22:53:26 UTC  
> Url: https://github.com/boostorg/utility/pull/12#issuecomment-45810761  

> I was surprised that the general specialization didn't attempt perfect forwarding (or the next best thing in C++03) and passed arguments by value instead.  
  
The C++03 implementation should take arguments by `const&`, but that might break cases where a copy constructor takes a non-`const&`. I doubt that there is merit in continue supporting those cases, but that's orthogonal to the proposed changes.

---
