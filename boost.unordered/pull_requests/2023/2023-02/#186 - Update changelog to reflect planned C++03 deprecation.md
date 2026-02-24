# #186 Update changelog to reflect planned C++03 deprecation [Merged]

> Username: cmazakas  
> Created at: 2023-02-28 22:56:13 UTC  
> Updated at: 2023-03-01 17:53:29 UTC  
> Merged at: 2023-03-01 17:53:24 UTC  
> Closed at: 2023-03-01 17:53:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/186  

As voted on, 1.84.0 will no longer support C++03.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-02-28 23:06:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/186#issuecomment-1449064348  

An automated preview of the documentation is available at [https://186.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://186.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-02-28 23:51:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/186#issuecomment-1449099913  

An automated preview of the documentation is available at [https://186.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://186.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-03-01 04:33:58 UTC  
> Updated_at: 2023-03-01 17:39:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/186#issuecomment-1449325607  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/186?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#186](https://codecov.io/gh/boostorg/unordered/pull/186?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0a3ac26) into [develop](https://codecov.io/gh/boostorg/unordered/commit/c64ed1caa3845c3ad08f18e9a281c2ffcf2c7afe?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c64ed1c) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 0a3ac26 differs from pull request most recent head dc63438. Consider uploading reports for the commit dc63438 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/186/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/186?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #186   +/-   ##  
========================================  
  Coverage    97.90%   97.90%             
========================================  
  Files           89       89             
  Lines        13777    13777             
========================================  
  Hits         13488    13488             
  Misses         289      289             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/186?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/186?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c64ed1c...dc63438](https://codecov.io/gh/boostorg/unordered/pull/186?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 4 [Commented]

> Username: joaquintides  
> Created_at: 2023-03-01 15:36:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/186#pullrequestreview-1320104733  

Does this `{cpp}` macro really work? I've always used plain `C++` and it didn't seem to clutter the syntax.

---

## Comment 5

> Username: cmazakas  
> Created_at: 2023-03-01 15:52:09 UTC  
> Updated_at: 2023-03-01 15:52:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/186#issuecomment-1450380271  

> Does this `{cpp}` macro really work? I've always used plain `C++` and it didn't seem to clutter the syntax.  
  
Yeah, it does.  
  
It's funny, I had actually completely forgotten about the macro's existence when I made my initial draft.  
  
It seems needed because `++` is special syntax to the asciidoctor parser. The initial rendering when I had used `C++03` came out looking like `C03` because it was confusing the `++` for something else. Using the macro renders `C++` properly in the text.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-03-01 15:59:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/186#issuecomment-1450393479  

`++` is literal escape (`++ ... things here where syntax is ignored ... ++`). Very funny of them.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-03-01 17:46:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/186#issuecomment-1450582768  

An automated preview of the documentation is available at [https://186.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://186.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
