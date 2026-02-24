# #55 styling fixes for quickbook docs [Merged]

> Username: cdw9  
> Created at: 2024-12-03 16:53:59 UTC  
> Updated at: 2025-01-20 17:46:06 UTC  
> Merged at: 2024-12-04 01:01:52 UTC  
> Closed at: 2024-12-04 01:01:52 UTC  
> Url: https://github.com/boostorg/boostlook/pull/55  

https://github.com/boostorg/boostlook/issues/54

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-12-03 17:03:20 UTC  
> Url: https://github.com/boostorg/boostlook/pull/55#issuecomment-2515115969  

An automated preview of the documentation is available at [https://55.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://55.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 2 [Commented]

> Username: julioest  
> Created_at: 2024-12-03 20:29:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/55#pullrequestreview-2476690349  

Looks great so far!   
  
A couple of things to address:  
- adjust the large horizontal spacing between code blocks, tables, and other elements  
- update the spirit nav icons to match Learn Docs  
- align the `.title` with the spirit nav

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-12-03 20:48:27 UTC  
> Url: https://github.com/boostorg/boostlook/pull/55#issuecomment-2515521891  

An automated preview of the documentation is available at [https://55.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://55.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 4 [Changes requested]

> Username: julioest  
> Created_at: 2024-12-03 21:01:32 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boostlook/pull/55#pullrequestreview-2476744993  

Awesome! Looks much better  
  
Only thing left is updating spirit nav icons to match Antora.  
  
Lmk if you need any help!

---

## Review 5 [Commented]

> Username: rbbeeston  
> Created_at: 2024-12-03 22:16:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/55#pullrequestreview-2476864450  

📁 boostlook.css

```diff
 513 |   padding: 0.25em 0.55em;
 510 |-   font-weight: 550;
 514 | }
```

> Username: rbbeeston  
> Created_at: 2024-12-03 22:16:24 UTC  
> Updated_at: 2024-12-03 22:16:25 UTC  
> Url: https://github.com/boostorg/boostlook/pull/55#discussion_r1868436394  

Curious why the font-weight was removed.

> Username: cdw9  
> Created_at: 2024-12-03 23:11:39 UTC  
> Updated_at: 2024-12-03 23:11:40 UTC  
> Url: https://github.com/boostorg/boostlook/pull/55#discussion_r1868486023  

I removed this so the default weight of 700 will display


---

## Review 6 [Commented]

> Username: rbbeeston  
> Created_at: 2024-12-03 22:18:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostlook/pull/55#pullrequestreview-2476867121  

one comment, but ok to merge once Julio's items are addressed.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-12-03 23:18:20 UTC  
> Url: https://github.com/boostorg/boostlook/pull/55#issuecomment-2515787690  

An automated preview of the documentation is available at [https://55.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://55.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 8 [Approved]

> Username: rbbeeston  
> Created_at: 2024-12-03 23:42:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/55#pullrequestreview-2476972857  

I think this is fine to merge now

---

## Comment 9

> Username: julioest  
> Created_at: 2024-12-04 01:38:01 UTC  
> Url: https://github.com/boostorg/boostlook/pull/55#issuecomment-2515974212  

Hey @cdw9 - it seems that the pre-commit hook didn't run based on the CI job.  
  
Run a `pre-commit install` locally, so next time the hook catches and fixes the errors in the CSS file. Thanks!

---
