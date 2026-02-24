# #228 fix name hiding bug #12497 [Closed]

> Username: sehe  
> Created at: 2016-12-13 23:45:32 UTC  
> Updated at: 2016-12-18 22:34:39 UTC  
> Closed at: 2016-12-18 22:34:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/228  

::boost::long_long_type was hidden by ::boost::spirit::long_long_type silently making the specialization inactive  
  
See also https://svn.boost.org/trac/boost/ticket/12497, thanks @K-ballo for the leg-work there, that made it much easier to spot.

---

## Comment 1

> Username: sehe  
> Created_at: 2016-12-13 23:52:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/228#issuecomment-266899662  

Oh darn. Only after reviewing the effective change against develop I see that @K-ballo happens to have fixed this a month ago, with only the spelling difference.

---

## Comment 2

> Username: djowel  
> Created_at: 2016-12-14 00:30:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/228#issuecomment-266906079  

Feel free to close this is it is indeed already fixed.

---

## Comment 3

> Username: sehe  
> Created_at: 2016-12-18 22:34:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/228#issuecomment-267852096  

What's more, <:: is a trigraph so for pre-c++11 compilers that could have been problematic

---
