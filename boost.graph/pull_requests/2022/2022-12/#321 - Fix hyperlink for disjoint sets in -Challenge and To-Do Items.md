# #321 Fix hyperlink for disjoint sets in "Challenge and To-Do Items" [Merged]

> Username: derek-mcblane  
> Created at: 2022-12-28 03:31:00 UTC  
> Updated at: 2023-09-12 03:32:24 UTC  
> Merged at: 2023-09-12 03:32:15 UTC  
> Closed at: 2023-09-12 03:32:15 UTC  
> Url: https://github.com/boostorg/graph/pull/321  

The link text was messed up because the tag isn't closed.

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-03-09 23:58:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/321#pullrequestreview-1333985862  

📁 doc/challenge.html

```diff
  39 | 
  40 |-    <li><tt>disjoint_sets</tt> (see <a href="disjoint_sets.html">)</li>
  40 |+    <li><tt>disjoint_sets</tt> (see <a href="disjoint_sets.html">Disjoint Sets</a>)</li>
```

> Username: jeremy-murphy  
> Created_at: 2023-03-09 23:58:10 UTC  
> Updated_at: 2023-03-09 23:58:11 UTC  
> Url: https://github.com/boostorg/graph/pull/321#discussion_r1131774841  

I'm fairly naive about HTML, so can you explain to me why there needs to be a `</a>`? I thought the opening `<a` was closed by the 'naked' `>`?

> Username: derek-mcblane  
> Created_at: 2023-04-25 07:42:52 UTC  
> Updated_at: 2023-04-25 07:42:53 UTC  
> Url: https://github.com/boostorg/graph/pull/321#discussion_r1176130805  

I’m also not too familiar with HTML. Here’s a [page](https://www.w3schools.com/html/html_links.asp) giving basic syntax for the <a> tag.  
  
I have tested that the change fixes the link text.


---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2023-09-12 03:32:24 UTC  
> Url: https://github.com/boostorg/graph/pull/321#issuecomment-1714910415  

Thanks for fixing it!

---
