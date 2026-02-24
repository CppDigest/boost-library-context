# #65 Fix Error C4596: 'difference_type': illegal qualified name in member declaration [Merged]

> Username: ncook-hxgn  
> Created at: 2018-02-28 11:06:07 UTC  
> Updated at: 2018-07-11 14:32:08 UTC  
> Merged at: 2018-04-06 13:58:21 UTC  
> Closed at: 2018-04-06 13:58:21 UTC  
> Url: https://github.com/boostorg/range/pull/65  

Hi there,   
  
When compiling against boost 1.66.0 with VS 2017 Professional 15.5.6 and MSVC 14.1, a wild C4596 Illegal Qualified Name' appeared.  
  
The fix was a simple one, I hope I used the right branch - this is my first ever PR. We compile with /Wall and treat warning as errors..  
  
I hope this is useful.

---

## Comment 1

> Username: ncook-hxgn  
> Created_at: 2018-02-28 12:47:10 UTC  
> Url: https://github.com/boostorg/range/pull/65#issuecomment-369228909  

The CI doesn't test MSVC /Visual Studio and that's where I found this... ?

---

## Comment 2

> Username: ncook-hxgn  
> Created_at: 2018-02-28 13:19:18 UTC  
> Url: https://github.com/boostorg/range/pull/65#issuecomment-369236538  

Looks like I have massively failed the CI tests. Glad these were here.   
  
I could guard my change with a preprocessor expression such as  
#if defined(_MSC_VER) && _MSC_VER > 1911  
...  
#endif  
  
Does anyone have any advice on preferred course of action?

---

## Comment 3

> Username: ncook-hxgn  
> Created_at: 2018-02-28 16:21:43 UTC  
> Url: https://github.com/boostorg/range/pull/65#issuecomment-369293800  

:dancer:

---

## Review 4 [Commented]

> Username: Flamefire  
> Created_at: 2018-07-11 14:25:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/range/pull/65#pullrequestreview-136264445  

📁 include/boost/range/concepts.hpp

```diff
 255 |+  // MSVC 14.1 - avoid C4596: 'difference_type': illegal qualified name in member declaration
 256 |+  #if defined(_MSC_VER) && _MSC_VER >= 1912 
 257 |+              BOOST_DEDUCED_TYPENAME difference_type n;
```

> Username: Flamefire  
> Created_at: 2018-07-11 14:25:56 UTC  
> Updated_at: 2018-07-11 14:25:57 UTC  
> Url: https://github.com/boostorg/range/pull/65#discussion_r201710172  

There is a problem here: The `BOOST_DEDUCED_TYPENAME` is superflous

> Username: ncook-hxgn  
> Created_at: 2018-07-11 14:28:15 UTC  
> Url: https://github.com/boostorg/range/pull/65#discussion_r201711135  

That't not all: #66

> Username: Flamefire  
> Created_at: 2018-07-11 14:32:08 UTC  
> Url: https://github.com/boostorg/range/pull/65#discussion_r201712710  

I've seen that. But I think the superflous part here is the reason for `error C3646: 'n' : unknown override specifier`. I mean `typename difference_type n`?


---
