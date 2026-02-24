# #72 improve boostlook.css maintainability  [Merged]

> Username: julioest  
> Created at: 2025-01-07 20:10:45 UTC  
> Updated at: 2025-01-08 22:19:38 UTC  
> Merged at: 2025-01-08 22:19:38 UTC  
> Closed at: 2025-01-08 22:19:38 UTC  
> Url: https://github.com/boostorg/boostlook/pull/72  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-01-07 20:16:33 UTC  
> Url: https://github.com/boostorg/boostlook/pull/72#issuecomment-2576145673  

An automated preview of the documentation is available at [https://72.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://72.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 2 [Approved]

> Username: rbbeeston  
> Created_at: 2025-01-07 21:23:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/72#pullrequestreview-2535395682  

Great work labeling everything! next steps would be combining/simplifying classes where possible.

---

## Comment 3

> Username: cdw9  
> Created_at: 2025-01-07 22:53:09 UTC  
> Url: https://github.com/boostorg/boostlook/pull/72#issuecomment-2576377869  

Some differences I'm seeing with these changes (screenshots are from the Beast and URL readmes):  
  
Tables are missing borders:  
<img width="1558" alt="Screenshot 2025-01-07 at 16 19 36" src="https://github.com/user-attachments/assets/f6f592b1-7868-42a9-ac75-40e2f06215d4" />  
  
Font weight is more bold, `<strong>` elements appear at the same weight, and h2 has no border:  
<img width="1558" alt="Screenshot 2025-01-07 at 16 20 44" src="https://github.com/user-attachments/assets/cc0d8eff-b847-43d9-913f-ffff2f29692b" />  
  
`<pre>` blocks have no background color like `<code>`  
<img width="1495" alt="Screenshot 2025-01-07 at 16 28 22" src="https://github.com/user-attachments/assets/e766774f-7499-45f5-ba7a-331d6e347a96" />

---

## Review 4 [Changes requested]

> Username: rbbeeston  
> Created_at: 2025-01-08 00:25:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boostlook/pull/72#pullrequestreview-2535606965  

Missed this before, but @cdw9 comments helped point them out  
the  `.boostlook#libraryReadMe` items should have a space, as in `.boostlook #libraryReadMe`

---

## Comment 5

> Username: julioest  
> Created_at: 2025-01-08 17:37:04 UTC  
> Url: https://github.com/boostorg/boostlook/pull/72#issuecomment-2578246260  

Thanks for looking into & the screenshots. Looking into these...  
  
Also, @rbbeeston, we're not able to do that since both class + id are on the same div

---

## Review 6 [Approved]

> Username: rbbeeston  
> Created_at: 2025-01-08 18:51:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/72#pullrequestreview-2537940880  

LGTM!

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-01-08 18:56:40 UTC  
> Url: https://github.com/boostorg/boostlook/pull/72#issuecomment-2578399192  

An automated preview of the documentation is available at [https://72.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://72.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2025-01-08 19:16:43 UTC  
> Url: https://github.com/boostorg/boostlook/pull/72#issuecomment-2578439356  

An automated preview of the documentation is available at [https://72.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://72.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2025-01-08 20:31:38 UTC  
> Url: https://github.com/boostorg/boostlook/pull/72#issuecomment-2578600637  

An automated preview of the documentation is available at [https://72.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://72.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 10 [Approved]

> Username: rbbeeston  
> Created_at: 2025-01-08 22:01:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/72#pullrequestreview-2538372267  

LGTM!

---
