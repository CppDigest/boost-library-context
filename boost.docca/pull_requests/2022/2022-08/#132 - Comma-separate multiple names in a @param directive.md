# #132 Comma-separate multiple names in a @param directive [Merged]

> Username: evanlenz  
> Created at: 2022-08-31 22:13:47 UTC  
> Updated at: 2023-01-31 06:31:27 UTC  
> Merged at: 2022-08-31 23:15:15 UTC  
> Closed at: 2022-08-31 23:15:15 UTC  
> Url: https://github.com/boostorg/docca/pull/132  

fix #131

---

## Review 1 [Commented]

> Username: evanlenz  
> Created_at: 2022-08-31 22:15:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/docca/pull/132#pullrequestreview-1092570875  

📁 include/docca/base-stage1.xsl

```diff
 322 |               <td>
 323 |-                 <code>
 324 |-                   <!-- ASSUMPTION: <parameternamelist> only ever has one <parametername> child -->
```

> Username: evanlenz  
> Created_at: 2022-08-31 22:15:56 UTC  
> Updated_at: 2022-08-31 22:15:57 UTC  
> Url: https://github.com/boostorg/docca/pull/132#discussion_r960068379  

@vinniefalco It turned out to be a bad assumption, but at least I felt sufficiently unsettled about it to document it! 😂

> Username: vinniefalco  
> Created_at: 2022-08-31 23:12:48 UTC  
> Updated_at: 2022-08-31 23:12:49 UTC  
> Url: https://github.com/boostorg/docca/pull/132#discussion_r960094570  

lmao... :)


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-31 22:19:17 UTC  
> Url: https://github.com/boostorg/docca/pull/132#issuecomment-1233477337  

An automated preview of the documentation is available at [https://132.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://132.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-08-31 23:13:01 UTC  
> Url: https://github.com/boostorg/docca/pull/132#issuecomment-1233538261  

Is "issue132" in the table of contents?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-08-31 23:13:18 UTC  
> Url: https://github.com/boostorg/docca/pull/132#issuecomment-1233538518  

OH 131

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-08-31 23:13:39 UTC  
> Url: https://github.com/boostorg/docca/pull/132#issuecomment-1233538801  

BRO.... you don't understand how good this is

---

## Comment 6

> Username: evanlenz  
> Created_at: 2022-09-01 20:03:58 UTC  
> Url: https://github.com/boostorg/docca/pull/132#issuecomment-1234723628  

> BRO.... you don't understand how good this is  
  
Glad you like it, bro! 😁

---
