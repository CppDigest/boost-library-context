# #192 improve examples [Merged]

> Username: alandefreitas  
> Created at: 2022-06-08 16:21:10 UTC  
> Updated at: 2022-06-10 22:50:47 UTC  
> Merged at: 2022-06-09 20:07:22 UTC  
> Closed at: 2022-06-09 20:07:23 UTC  
> Url: https://github.com/boostorg/url/pull/192  

fix #190, fix #189, fix #186, fix #185, fix #194  
  
This PR synthesizes all suggestions mentioned in these issues.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-06-08 16:29:49 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150137625  

An automated preview of the documentation is available at [https://192.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://192.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-06-08 16:35:27 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150142984  

There's too many "Note" callouts and occurrences of the word "Note" in exposition on the Quick Look page. Callouts have to be used sparingly or they lose their value in drawing the user's attention.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-06-08 16:36:01 UTC  
> Updated_at: 2022-06-08 16:37:46 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150143501  

@akrzemi1 needs to review the doc preview, please?

---

## Comment 4

> Username: akrzemi1  
> Created_at: 2022-06-08 18:02:59 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150228261  

> @akrzemi1 needs to review the doc preview, please?  
  
I would be happy to. But is there a way to see the rendered effect of these changes?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-06-08 18:11:19 UTC  
> Updated_at: 2022-06-08 18:11:44 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150236055  

> I would be happy to. But is there a way to see the rendered effect of these changes?  
  
Yes, see: https://github.com/CPPAlliance/url/pull/192#issuecomment-1150137625  
  
By the way, if you want documentation previews on pull requests for your own repos, we can set that up (ask @sdarwin on Slack)

---

## Comment 6

> Username: akrzemi1  
> Created_at: 2022-06-08 18:14:45 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150239080  

BTW, as you change the examples, I think there is an error in the third table in section ["Accessing"](https://master.url.cpp.al/url/quick_look.html#url.quick_look.accessing), the one below text "To differentiate between empty and absent components, we can use functions such as has_fragment". The source code doesn't have any instruction that would print "has fragment 1".

---

## Review 7 [Commented]

> Username: akrzemi1  
> Created_at: 2022-06-08 18:25:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/192#pullrequestreview-1000240355  

📁 doc/qbk/2.0.quicklook.qbk

```diff
  99 | 
 100 | [note
 101 |     It is worth noting that __parse_uri__ allocates no memory and throws no exceptions.
```

> Username: akrzemi1  
> Created_at: 2022-06-08 18:25:46 UTC  
> Url: https://github.com/boostorg/url/pull/192#discussion_r892724837  

This is not true. According to synopsis, this function throws `length_error` when  the input string is too long.   
I think that the essential guarantee is that:  
* The function does not allocate memory  
* The situation where the input does not match the URL grammar are reporter through `result<>` rather than exceptions  
* Any other errors (such as excessive length) are reported via exceptions.

> Username: akrzemi1  
> Created_at: 2022-06-08 18:35:50 UTC  
> Url: https://github.com/boostorg/url/pull/192#discussion_r892733416  

There is another issue with this note. Because `url_view` is non-owing, it cannot guarantee that it always represents a valid URI, because the contents of the original string may be changed externally:  
https://godbolt.org/z/x4nzGPb9G


---

## Comment 8

> Username: akrzemi1  
> Created_at: 2022-06-08 18:39:47 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150262606  

I have put three somewhat unrelated comments. Other than that, I think the intro page is now way smoother. Thank you!

---

## Comment 9

> Username: alandefreitas  
> Created_at: 2022-06-08 19:30:45 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150315156  

Amended!

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-06-08 19:44:49 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150332698  

An automated preview of the documentation is available at [https://192.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://192.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-06-08 20:19:48 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150367822  

An automated preview of the documentation is available at [https://192.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://192.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 12

> Username: alandefreitas  
> Created_at: 2022-06-08 20:20:40 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150368583  

@vinniefalco, @akrzemi1   
I guess this is good, right?

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-06-08 20:54:51 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150405648  

An automated preview of the documentation is available at [https://192.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://192.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 14

> Username: akrzemi1  
> Created_at: 2022-06-08 21:11:22 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150419407  

> I guess this is good, right?  
  
Very good! Thanks.  
  
If I could have a tiny suggestion, this text:  
  
> Only std::length_error, when s.size() > url_view::max_size, is reported via exceptions.   
  
It may bee too much details for an intro page. The user will learn it from the synopsis, when they need to.

---

## Comment 15

> Username: codecov[bot]  
> Created_at: 2022-06-08 23:45:14 UTC  
> Updated_at: 2022-06-09 20:07:13 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1150521812  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/192?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#192](https://codecov.io/gh/CPPAlliance/url/pull/192?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (94b723f) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/59545321e292f0a8792708a46071f80d5dea3034?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (5954532) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 94b723f differs from pull request most recent head 2e677d2. Consider uploading reports for the commit 2e677d2 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/192/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #192   +/-   ##  
========================================  
  Coverage    96.73%   96.73%             
========================================  
  Files          119      119             
  Lines         6056     6056             
========================================  
  Hits          5858     5858             
  Misses         198      198             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/192/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/192?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/192?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [5954532...2e677d2](https://codecov.io/gh/CPPAlliance/url/pull/192?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 16

> Username: akrzemi1  
> Created_at: 2022-06-09 19:09:24 UTC  
> Url: https://github.com/boostorg/url/pull/192#issuecomment-1151504552  

The URL under `std::regular` at https://192.url.prtest.cppalliance.org/libs/url/doc/html/url/quick_look.html is wrong.

---
