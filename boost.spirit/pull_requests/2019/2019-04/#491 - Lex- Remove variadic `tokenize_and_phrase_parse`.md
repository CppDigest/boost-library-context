# #491 Lex: Remove variadic `tokenize_and_phrase_parse` [Merged]

> Username: Kojoley  
> Created at: 2019-04-18 23:54:18 UTC  
> Updated at: 2019-04-19 10:13:27 UTC  
> Merged at: 2019-04-19 10:13:22 UTC  
> Closed at: 2019-04-19 10:13:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/491  

They were added 10 years ago and did not compile all the time due to bugs spotted during warning elimination work, but the main reason to remove them is that there was not a single report about the issues.  
  
Closes #490

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2019-04-19 01:23:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/491#pullrequestreview-228563955  

LGTM, thanks!

---
