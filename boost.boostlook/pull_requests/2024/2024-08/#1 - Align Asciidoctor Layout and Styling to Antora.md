# #1 Align Asciidoctor Layout and Styling to Antora [Merged]

> Username: julioest  
> Created at: 2024-08-09 23:54:56 UTC  
> Updated at: 2024-08-26 21:52:53 UTC  
> Merged at: 2024-08-26 21:52:53 UTC  
> Closed at: 2024-08-26 21:52:53 UTC  
> Url: https://github.com/boostorg/boostlook/pull/1  

### Description  
This PR aligns the Asciidoctor layout and styling with our latest Antora design.  
  
### Issues:  
- ~The table elements overflow when the container becomes narrower. (Still working on a fix)~

---

## Comment 1

> Username: julioest  
> Created_at: 2024-08-21 21:15:02 UTC  
> Url: https://github.com/boostorg/boostlook/pull/1#issuecomment-2303025850  

@vinniefalco Here's a preview of the latest  
<img width="1624" alt="image" src="https://github.com/user-attachments/assets/60566631-fe64-4667-8062-6bbbde1cb2b4">

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2024-08-23 19:23:47 UTC  
> Url: https://github.com/boostorg/boostlook/pull/1#issuecomment-2307683993  

Exciting :)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-23 19:24:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/1#pullrequestreview-2257963121  

📁 boostlook.css

```diff
 188 |+ }
 189 |+ 
 190 |+ @media screen and (min-width: 768px) {
```

> Username: vinniefalco  
> Created_at: 2024-08-23 19:24:14 UTC  
> Url: https://github.com/boostorg/boostlook/pull/1#discussion_r1729424144  

This should be using container query

> Username: julioest  
> Created_at: 2024-08-26 21:50:45 UTC  
> Updated_at: 2024-08-26 21:51:28 UTC  
> Url: https://github.com/boostorg/boostlook/pull/1#discussion_r1731865208  

Tried making it work with the `@container` query, I'm not too familiar with it btw. I'm going to revisit how we can leverage that query in future PRs. We'd have to think about this more.


---
