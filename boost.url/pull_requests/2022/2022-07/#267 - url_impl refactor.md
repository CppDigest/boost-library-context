# #267 url_impl refactor [Merged]

> Username: vinniefalco  
> Created at: 2022-07-28 15:25:18 UTC  
> Updated at: 2022-08-02 17:43:37 UTC  
> Merged at: 2022-07-30 02:19:13 UTC  
> Closed at: 2022-07-30 02:19:13 UTC  
> Url: https://github.com/boostorg/url/pull/267  

This moves the guts of URLs to a private implementation detail and refactors parsing

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-07-28 15:35:03 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1198318123  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-07-28 16:05:01 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1198349145  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-07-28 17:45:00 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1198449514  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-07-28 23:49:58 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1198731790  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-07-29 00:04:59 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1198738424  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-07-29 00:39:58 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1198757580  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-07-29 01:34:58 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1198793746  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-07-29 14:19:58 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1199372709  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-07-29 14:59:55 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1199469888  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-07-29 19:24:52 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1199877145  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-07-29 19:44:54 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1199891361  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-07-29 23:24:51 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1200029814  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-07-29 23:39:45 UTC  
> Url: https://github.com/boostorg/url/pull/267#issuecomment-1200034895  

An automated preview of the documentation is available at [https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://267.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 14 [Commented]

> Username: alandefreitas  
> Created_at: 2022-07-30 01:49:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/267#pullrequestreview-1056170267  

Everything is much simpler. I couldn't find anything very controversial. Maybe because the PR is removing a lot of code.

📁 include/boost/url/authority_view.hpp

```diff
  81 |-         id_end          // one past the end
  82 |-     };
  75 |+     detail::url_impl u_;
```

> Username: alandefreitas  
> Created_at: 2022-07-30 01:36:22 UTC  
> Updated_at: 2022-07-30 01:49:42 UTC  
> Url: https://github.com/boostorg/url/pull/267#discussion_r933704747  

:open_mouth: So much simpler

> Username: vinniefalco  
> Created_at: 2022-07-30 02:10:49 UTC  
> Url: https://github.com/boostorg/url/pull/267#discussion_r933708134  

in a sense yes, since `authority_view` just recycles the same id enum and struct of offsets and state. Much better than the bulk copy-paste I did originally


---
