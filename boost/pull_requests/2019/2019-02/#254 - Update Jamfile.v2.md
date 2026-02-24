# #254 Update Jamfile.v2 [Closed]

> Username: Anupam-tripathi-zz  
> Created at: 2019-02-28 11:30:59 UTC  
> Updated at: 2019-05-18 14:21:40 UTC  
> Closed at: 2019-05-18 14:21:40 UTC  
> Url: https://github.com/boostorg/boost/pull/254  



---

## Comment 1

> Username: mclow  
> Created_at: 2019-02-28 14:37:09 UTC  
> Url: https://github.com/boostorg/boost/pull/254#issuecomment-468295454  

A few comments:  
* We don't take pull requests into `master`. We update `develop`, watch the tests, and then merge into `master`. See https://svn.boost.org/trac10/wiki/StartModMaint  
* The title of the P/R is not helpful. What does this pull request accomplish?  
* This pull request consists of the removal of two blank lines. Why?

---

## Review 2 [Commented]

> Username: mohitm15  
> Created_at: 2019-04-22 13:53:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/254#pullrequestreview-229069631  

📁 doc/Jamfile.v2

```diff
  23 | import "class" : is-a ;
  24 |- 
  24 | path-constant BOOST_DOC : . ;
```

> Username: mohitm15  
> Created_at: 2019-04-22 13:53:06 UTC  
> Updated_at: 2019-04-22 13:54:43 UTC  
> Url: https://github.com/boostorg/boost/pull/254#discussion_r277296352  

@Anupam-tripathi You only removed two blank lines with an insignificant title of PR. This is **not** a good practice. I suggest you to read **contribution guidelines** of boost and then work on an issue.  
You also not mentioned the issue it resolves. Avoid from making vague changes.


---

## Comment 3

> Username: glenfe  
> Created_at: 2019-05-18 14:21:40 UTC  
> Url: https://github.com/boostorg/boost/pull/254#issuecomment-493680715  

This makes no sense to me.

---
