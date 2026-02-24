# #191 Repair plain_token_range and add tests [Merged]

> Username: jefftrull  
> Created at: 2016-05-17 01:14:58 UTC  
> Updated at: 2016-08-05 22:22:45 UTC  
> Merged at: 2016-08-05 22:19:09 UTC  
> Closed at: 2016-08-05 22:19:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/191  

plain_token_range has a bug where it really only checks for equality against the bottom of the range supplied.  This patch restores the intended behavior.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-05-17 05:23:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/191#issuecomment-219622248  

Would it make sense to have a test for this? I might have to defer this one to @hkaiser. Thoughts?

---

## Comment 2

> Username: hkaiser  
> Created_at: 2016-08-05 21:55:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/191#issuecomment-237975441  

LGTM! thanks!

---
