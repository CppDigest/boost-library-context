# #12 Refactor CSS variables and styles for light and dark themes [Merged]

> Username: julioest  
> Created at: 2024-09-16 19:10:12 UTC  
> Updated at: 2024-09-24 20:11:20 UTC  
> Merged at: 2024-09-24 20:11:20 UTC  
> Closed at: 2024-09-24 20:11:20 UTC  
> Url: https://github.com/boostorg/boostlook/pull/12  

- remove underline links  
- tidy CSS variables  
- adjust font-stretch to match asciidoctor  
- adjust link colors and hover states  
- add caret icon for current page in nav  
- update font family names to avoid integration conflicts  
  
fixes several items on #13

---

## Review 1 [Changes requested]

> Username: rbbeeston  
> Created_at: 2024-09-24 18:55:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boostlook/pull/12#pullrequestreview-2326090107  

📁 boostlook.css

```diff
  40 |-   --light-bl-table-border-color: rgb(232, 232, 232);
  41 |-   --light-bl-table-head-background: rgb(248, 248, 248);
  38 |+   --bl-primary-orange: rgb(255, 159, 0);
```

> Username: rbbeeston  
> Created_at: 2024-09-24 18:44:54 UTC  
> Updated_at: 2024-09-24 18:55:48 UTC  
> Url: https://github.com/boostorg/boostlook/pull/12#discussion_r1773849611  

primary, secondary, etc. colors shouldn't have the color as part of the name, as it could change. --bl-primary-color is fine

> Username: julioest  
> Created_at: 2024-09-24 19:58:31 UTC  
> Url: https://github.com/boostorg/boostlook/pull/12#discussion_r1774001024  

Resolved in latest commit

---

📁 boostlook.css

```diff
  56 |+   --light-bl-pasteboard-color: rgb(209, 213, 219);
  57 |+   --light-bl-pre-background: rgb(248, 248, 248);
  58 |+   --light-bl-primary-orange: rgb(255, 159, 0);
```

> Username: rbbeeston  
> Created_at: 2024-09-24 18:54:04 UTC  
> Updated_at: 2024-09-24 18:55:48 UTC  
> Url: https://github.com/boostorg/boostlook/pull/12#discussion_r1773862158  

same as above

> Username: julioest  
> Created_at: 2024-09-24 19:58:36 UTC  
> Url: https://github.com/boostorg/boostlook/pull/12#discussion_r1774001113  

Resolved in latest commit


---

## Review 2 [Approved]

> Username: rbbeeston  
> Created_at: 2024-09-24 20:10:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/12#pullrequestreview-2326339876  

Ship it!

---
