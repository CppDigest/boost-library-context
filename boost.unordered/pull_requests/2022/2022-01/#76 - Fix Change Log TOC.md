# #76 Fix Change Log TOC [Closed]

> Username: cmazakas  
> Created at: 2022-01-07 23:13:14 UTC  
> Updated at: 2022-01-09 15:12:07 UTC  
> Closed at: 2022-01-09 01:44:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/76  

By updating the Jamfile, we can get the Unordered change log to render like the one in Core.

---

## Comment 1

> Username: glenfe  
> Created_at: 2022-01-08 18:10:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/76#issuecomment-1008085994  

I would probably have kept the changelog using [heading] instead of [section] and leave the TOC depth as it is now.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-01-08 18:13:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/76#issuecomment-1008087245  

Yep. Let's just get back to `[h2]` as it was before [the change](https://github.com/boostorg/unordered/commit/8ce147dcbdddc520c77f327a410eaa8b234e2a64).

---

## Comment 3

> Username: glenfe  
> Created_at: 2022-01-08 18:17:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/76#issuecomment-1008089467  

[heading] might be better than [h2] because it automatically chooses the heading size based on the current section depth.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-01-09 01:44:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/76#issuecomment-1008209640  

Since the Unordered docs are of the "integrated" variety, the local chunking parameter doesn't actually apply. They currently [look like this](https://www.boost.org/doc/libs/develop/doc/html/unordered.html). I think that we don't want this, so using `[heading]` seems the way to go.

---
