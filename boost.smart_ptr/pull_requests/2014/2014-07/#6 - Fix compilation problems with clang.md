# #6 Fix compilation problems with clang [Merged]

> Username: Lastique  
> Created at: 2014-07-16 12:43:41 UTC  
> Updated at: 2014-07-18 18:09:24 UTC  
> Merged at: 2014-07-16 13:31:22 UTC  
> Closed at: 2014-07-16 13:31:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/6  

Apparently, clang presents itself as gcc 4.2 even though it supports the final version of rvalue references. Restrict the workaround to gcc only.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-07-18 08:29:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/6#issuecomment-49407292  

The tests are all green on develop. Do you think we could merge it to master?

---

## Comment 2

> Username: pdimov  
> Created_at: 2014-07-18 18:09:24 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/6#issuecomment-49462179  

Let's follow the release procedures and not do changes to master unless they fix a critical bug report against the beta.

---
