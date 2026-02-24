# #11 make sub_match usable with Boost.Range [Closed]

> Username: muggenhor  
> Created at: 2015-03-03 19:15:31 UTC  
> Updated at: 2015-03-19 19:51:21 UTC  
> Closed at: 2015-03-19 19:51:21 UTC  
> Url: https://github.com/boostorg/regex/pull/11  

This implements this suggestion, i.e. it makes sub_match usable as a range in the least intrusive way: https://svn.boost.org/trac/boost/ticket/11036

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-03-08 17:45:52 UTC  
> Url: https://github.com/boostorg/regex/pull/11#issuecomment-77763840  

Thanks for the patch - as noted in the issue wouldn't begin() and end() member functions be a better bet?

---

## Comment 2

> Username: muggenhor  
> Created_at: 2015-03-08 22:30:06 UTC  
> Updated_at: 2015-03-08 22:30:34 UTC  
> Url: https://github.com/boostorg/regex/pull/11#issuecomment-77778910  

I did it the same as is done in xpressive's sub_match where I believe it was done like that because of compatibility with the C++ standard (can't verify that last as I don't have a copy of the latest C++ standard handy).  
  
Myself I don't really have a preference in either direction.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-03-19 19:51:21 UTC  
> Url: https://github.com/boostorg/regex/pull/11#issuecomment-83737607  

Fixed slightly differently in Git develop, closing.

---
