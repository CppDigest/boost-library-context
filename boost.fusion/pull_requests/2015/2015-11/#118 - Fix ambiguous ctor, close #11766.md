# #118 Fix ambiguous ctor, close #11766 [Merged]

> Username: Flast  
> Created at: 2015-11-30 16:31:48 UTC  
> Updated at: 2016-01-08 00:31:25 UTC  
> Merged at: 2016-01-07 21:39:51 UTC  
> Closed at: 2016-01-07 21:39:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/118  

Use `fusion::void_` instead of `void*` in SFINAE expression because it is ambiguous with forwarding ctor if second (and third) element is `void*`.  
  
close [#11766](https://svn.boost.org/trac/boost/ticket/11766)

---

## Comment 1

> Username: vtnerd  
> Created_at: 2015-11-30 16:36:24 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-160681771  

Should there be a custom type for this purpose in detail?

---

## Comment 2

> Username: djowel  
> Created_at: 2015-11-30 22:14:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-160778922  

What do you suggest, @vtnerd ?

---

## Comment 3

> Username: vtnerd  
> Created_at: 2015-12-01 00:55:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-160812177  

`fusion::void_` is a valid type that can used in a fusion sequence, so the problem with `void*` could exist for `fusion::void_` too. `fusion::void_` is not listed in the documentation, but _is_ in the top-level fusion namespace.  I was wondering whether a new type in detail (`detail::sfinae` or similar) should be created for this purpose, as it couldn't break any existing code, and would clearly indicate something that clients of the library should not be using.  
  
Admittedly, using `fusion::void_` intentionally in a sequence is likely rare, and even more so for this particular constructor.

---

## Comment 4

> Username: Flast  
> Created_at: 2015-12-01 07:14:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-160879985  

> `fusion::void_` is a valid type that can used in a fusion sequence, so the problem with void\* could exist for `fusion::void_` too.  
  
AFAIU, `fusion::void_` cannot appear as an element within fusion container: http://melpon.org/wandbox/permlink/waKDoHaE6KDhkKxI.  
In 1.61 (yes, it's not a typo), variadics based `fusion::vector` will stop treating `fusion::void_` specially and `fusion::void_` can be appeared within containers element, but I believe it don't mean accepting such `fusion::void_` usage.

---

## Comment 5

> Username: vtnerd  
> Created_at: 2015-12-01 14:34:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-160984967  

You're right, I was only thinking of the C++11 case which doesn't even exist _yet_. Whoops. Well something to think about for 1.61 then, if at all.

---

## Comment 6

> Username: Flast  
> Created_at: 2015-12-10 11:07:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-163580313  

> Well something to think about for 1.61 then, if at all.  
  
OK, I re-wrote with detail type.

---

## Comment 7

> Username: Flast  
> Created_at: 2016-01-05 11:08:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-168973313  

Any comments?

---

## Comment 8

> Username: djowel  
> Created_at: 2016-01-05 22:14:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-169150765  

Any more thoughts before I merge, vtnerd?

---

## Comment 9

> Username: vtnerd  
> Created_at: 2016-01-07 20:11:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-169792459  

No more comments. @Flast pointing out that no Fusion type could currently have `fusion::void_` as an element was enough. Although, I guess now in boost 1.61, `fusion::void_` can be used as an element successfully.

---

## Comment 10

> Username: Flast  
> Created_at: 2016-01-08 00:31:23 UTC  
> Url: https://github.com/boostorg/fusion/pull/118#issuecomment-169850352  

Thank you both!

---
