# #152 fix edit_segments suffix [Merged]

> Username: alandefreitas  
> Created at: 2022-03-25 16:38:44 UTC  
> Updated at: 2022-06-08 13:37:55 UTC  
> Merged at: 2022-03-25 17:00:24 UTC  
> Closed at: 2022-03-25 17:00:24 UTC  
> Url: https://github.com/boostorg/url/pull/152  

fix #151

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-25 16:43:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/152#pullrequestreview-921898208  

📁 include/boost/url/impl/url.ipp

```diff
1383 |+     "/path/to/the/file.txt"
1384 |+     -> replace "etc" as first segment
1385 |+     -> becomes "/etc" "/to/the/file.txt"
```

> Username: vinniefalco  
> Created_at: 2022-03-25 16:43:25 UTC  
> Url: https://github.com/boostorg/url/pull/152#discussion_r835442303  

oh this is nice


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-25 16:44:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/152#pullrequestreview-921899660  

📁 include/boost/url/impl/url.ipp

```diff
1353 |-     //if( nseg > 0 &&
1354 |-         //i1 + 1 < nseg_)
1389 |     if( nseg > 0 &&
```

> Username: vinniefalco  
> Created_at: 2022-03-25 16:44:43 UTC  
> Url: https://github.com/boostorg/url/pull/152#discussion_r835443324  

This is worth a comment now

> Username: alandefreitas  
> Created_at: 2022-03-25 16:48:50 UTC  
> Url: https://github.com/boostorg/url/pull/152#discussion_r835446600  

That thing above is the best comment I could come up with. I think another comment here would kind of replicate the conditions above.

> Username: alandefreitas  
> Created_at: 2022-03-25 16:49:31 UTC  
> Url: https://github.com/boostorg/url/pull/152#discussion_r835447169  

Actually, no. Just had an idea.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-25 16:44:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/152#pullrequestreview-921899835  

📁 include/boost/url/impl/url.ipp

```diff
1392 |+         nseg_ != 0)
1393 |     {
1394 |         suffix = 1;
```

> Username: vinniefalco  
> Created_at: 2022-03-25 16:44:53 UTC  
> Url: https://github.com/boostorg/url/pull/152#discussion_r835443451  

```  
// inserting at the beginning  
```

> Username: alandefreitas  
> Created_at: 2022-03-25 16:50:32 UTC  
> Updated_at: 2022-03-25 16:50:33 UTC  
> Url: https://github.com/boostorg/url/pull/152#discussion_r835447958  

This comment just helped me summarize the logic we need.


---
