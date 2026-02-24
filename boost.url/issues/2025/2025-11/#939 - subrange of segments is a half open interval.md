# #939 - subrange of segments is a half open interval [Closed]

> Username: vinniefalco  
> Created at: 2025-11-02 03:16:39 UTC  
> Updated at: 2025-11-05 00:07:18 UTC  
> Closed at: 2025-11-05 00:07:18 UTC  
> Url: https://github.com/boostorg/url/issues/939  

Given `"/path/to/the/file.txt"` as segments, the iterator range `(i0, i1)` representing `{ "path", "to" }` and the iterator range `(i1, i2)` representing `{ "the", "file.txt" }`,  then segments views constructed from those iterators should yield the strings `"/path/to`" and `"/the/file.txt"` respectively. Currently they are producing `"/path/to"` and `"the/file.txt"`. The range `(it0, it2)` should recover the original sequence but it is not; instead it is producing `"/path/tothe/file.txt"` which is incorrect.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2025-11-03 17:39:39 UTC  
> Updated at: 2025-11-03 17:39:52 UTC  
> Url: https://github.com/boostorg/url/issues/939#issuecomment-3481738498  

Understood.   
  
> The range (it0, it1) should recover the original sequence but it is not  
  
Could you share what the code looks like for that? 🙂  
  
For example, if you have two subviews of a string_view and try to “merge” them this way, MSVC will likely stop you with an assertion — since you can’t mix iterators from different views, even if they come from the same parent view and align perfectly at the edges. There’s no safe way for the compiler or the implementation to track that relationship automatically.  
  
The only safe approach is to materialize the views, and this limitation applies to all views 👍  
  
> is a half open interval  
  
Technically, it’s still a half-open interval because “/” isn’t an element of the view — it just represents the is-absolute property. But I see where your concern comes from 😊  
  
Currently, the idea is that it inherits the is-absolute property from the parent when the first iterator is begin(), and treats them as relative paths in all other cases (which makes sense given their nature).  
  
It would be great to have a clear definition of the expected return value here so I can move forward confidently ❤️ Especially since the master branch will be closing in two days.  
  
Here are a few options we can work with:  
  
1) A new subview always inherits the is-absolute property from the parent, regardless of where it is.  
2) A new subview is always considered absolute (includes “/”)  
3) Provide an API that lets the user decide whether the new subview should be absolute or relative (though this can’t apply to relative paths if the iterator is begin())  
4) Remove this function from the API if it’s too confusing or not useful — it has an O(n) cost similar to reparsing anyway  
  
Once we clarify which direction you prefer, I’ll move ahead right away 🙂

---

## Comment 2

> Username: vinniefalco  
> Created at: 2025-11-03 19:23:15 UTC  
> Url: https://github.com/boostorg/url/issues/939#issuecomment-3482135465  

if you have a view and you break it up into two non-overlapping iterator ranges which span the entire view, then the string which is produced by concatenating their buffers should be identical to the original string.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2025-11-03 19:25:08 UTC  
> Url: https://github.com/boostorg/url/issues/939#issuecomment-3482145024  

> Could you share what the code looks like for that?  
  
I cannot, because I haven't written it. And I think for the reasons you mentioned it will be difficult to do so. Yet I believe this assertion fails:  
```  
assert( v.buffer() == subview(it0,it1).buffer() + subview(it1,it2).buffer );  
```

---

## Comment 4

> Username: alandefreitas  
> Created at: 2025-11-03 20:30:16 UTC  
> Url: https://github.com/boostorg/url/issues/939#issuecomment-3482474965  

I believe these requirements suggest some expectations for views that only the materialized containers can handle 🙂  
  
Maybe @pdimov has some ideas about this💡

---

## Comment 5

> Username: alandefreitas  
> Created at: 2025-11-04 18:08:53 UTC  
> Updated at: 2025-11-04 18:09:02 UTC  
> Url: https://github.com/boostorg/url/issues/939#issuecomment-3487416401  

As discussed internally on 2025/11/03, we'll implement option 2: a new subview is absolute.
