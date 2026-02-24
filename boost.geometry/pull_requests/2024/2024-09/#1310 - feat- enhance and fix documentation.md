# #1310 feat: enhance and fix documentation [Merged]

> Username: barendgehrels  
> Created at: 2024-09-22 11:09:23 UTC  
> Updated at: 2024-12-14 11:13:09 UTC  
> Merged at: 2024-09-27 15:52:26 UTC  
> Closed at: 2024-09-27 15:52:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1310  

The registration macros were not clear enough. They were not yet stating that `longitude` should come first, and then `latitude`.   
  
Adding that, I encountered that the documentation procedure was broken on my Mac. This was caused in the end by this   
commit: https://github.com/boostorg/boostbook/commit/116c7b0f23b578ece6f5c530584a987f4db6947b - so I wonder why it can run in the main branches. See also https://github.com/boostorg/boostbook/issues/17  
  
Anyway, after fixing that locally I can generate the same documentation again. There were quite some warnings (by doxygen, or doxygen to qbk) which are fixed. Also some other unclarities are enhanced.  
  
Some pages were not used for already 10 years and are removed. This was in doxygen - but we use quickbook now.  
  
Documentation looks now like (some shots):  
  
<img width="1146" alt="image" src="https://github.com/user-attachments/assets/a9ef8325-fc8f-49b9-a2fd-d03bd0d8bf26">  
  
<img width="231" alt="image" src="https://github.com/user-attachments/assets/6f104c10-ab83-4acc-a0e8-ef6173d65521">

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2024-09-22 11:30:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1310#issuecomment-2366737794  

BTW, it's quite confusing that `b2` calls `make_qbk.py` and, vice versa, `make_qbk.py` can optionally call `b2` (in release mode).  
Which one is effectively used in release documentation?

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-24 17:07:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1310#pullrequestreview-2325890941  

📁 doc/debug_make_doc.sh

```diff
 145 |+ echo "=== Resulting contents of generated 'adapted.html', there should be sections"
 146 |+ cat html/geometry/reference/adapted.html | grep section
 147 |+ 
```

> Username: barendgehrels  
> Created_at: 2024-09-24 17:07:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1310#discussion_r1773732854  

@Lastique also for your information ('ll refer to it from elsewhere)


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2024-09-27 09:52:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1310#pullrequestreview-2333286611  

Looks good to me, thanks!

---
