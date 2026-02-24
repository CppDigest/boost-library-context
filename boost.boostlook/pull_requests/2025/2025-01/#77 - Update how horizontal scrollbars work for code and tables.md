# #77 Update how horizontal scrollbars work for code and tables [Merged]

> Username: cdw9  
> Created at: 2025-01-21 18:25:56 UTC  
> Updated at: 2025-01-23 17:28:40 UTC  
> Merged at: 2025-01-23 17:28:40 UTC  
> Closed at: 2025-01-23 17:28:40 UTC  
> Url: https://github.com/boostorg/boostlook/pull/77  

https://github.com/boostorg/website-v2/issues/1612  
For consistency across browsers and systems, set the overflow-x to auto, but using colors that match the background of the code block or table, so the scrollbar isn’t initially visible. On hover, the colors will change to the defined scrollbar colors. Note they do still display differently depending on each user’s systems settings, based on if they use classic or overlay scrollbars. Also moved scrollbar-related code to that section of the CSS file

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-01-21 18:31:43 UTC  
> Url: https://github.com/boostorg/boostlook/pull/77#issuecomment-2605464690  

An automated preview of the documentation is available at [https://77.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://77.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 2 [Commented]

> Username: rbbeeston  
> Created_at: 2025-01-21 19:13:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/77#pullrequestreview-2565495215  

Looks good to me, wait for Julio to sign off on it

---

## Review 3 [Approved]

> Username: julioest  
> Created_at: 2025-01-21 20:01:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/77#pullrequestreview-2565583227  

Scrollbars look good on my end (macOS - Chrome, Arc, Safari)

---
