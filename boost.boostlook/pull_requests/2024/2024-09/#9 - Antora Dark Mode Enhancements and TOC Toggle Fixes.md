# #9 Antora Dark Mode Enhancements and TOC Toggle Fixes [Merged]

> Username: julioest  
> Created at: 2024-09-12 20:35:42 UTC  
> Updated at: 2024-09-13 17:55:21 UTC  
> Merged at: 2024-09-13 17:55:21 UTC  
> Closed at: 2024-09-13 17:55:21 UTC  
> Url: https://github.com/boostorg/boostlook/pull/9  

- Improved dark mode styling for Antora with new variables and refined elements (code, quotes, tables)  
- Added Asciidoctor TOC toggle and fixed table overflow issues  
- Corrected font URL and optimized global styles

---

## Review 1 [Approved]

> Username: rbbeeston  
> Created_at: 2024-09-12 21:38:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/9#pullrequestreview-2301463820  

one minor nit, other than that it looks good!

---

## Review 2 [Commented]

> Username: rbbeeston  
> Created_at: 2024-09-12 21:39:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/9#pullrequestreview-2301469922  

📁 boostlook.css

```diff
  45 |+   --light-bl-quote-background: rgb(250, 250, 250);
  46 |+   --light-bl-quote-background: rgb(255, 255, 255);
  47 |+   --light-bl-code-background: rgb(255, 255, 255);
```

> Username: rbbeeston  
> Created_at: 2024-09-12 21:39:31 UTC  
> Url: https://github.com/boostorg/boostlook/pull/9#discussion_r1757621896  

why are there 2 of --light-bl-quote-background?

> Username: julioest  
> Created_at: 2024-09-13 03:20:37 UTC  
> Updated_at: 2024-09-13 03:20:38 UTC  
> Url: https://github.com/boostorg/boostlook/pull/9#discussion_r1758117745  

Good catch! Per our discussion, I'll fix this in the next one


---

## Comment 3

> Username: julioest  
> Created_at: 2024-09-13 14:49:23 UTC  
> Url: https://github.com/boostorg/boostlook/pull/9#issuecomment-2349140002  

@vinniefalco Please merge this when you can

---
