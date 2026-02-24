# #41 Fix mistakes [Merged]

> Username: ivanpanch  
> Created at: 2025-08-21 14:46:21 UTC  
> Updated at: 2025-08-22 07:59:54 UTC  
> Merged at: 2025-08-22 07:59:40 UTC  
> Closed at: 2025-08-22 07:59:40 UTC  
> Url: https://github.com/boostorg/bloom/pull/41  

I have corrected a few minor mistakes.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-08-21 14:55:51 UTC  
> Url: https://github.com/boostorg/bloom/pull/41#issuecomment-3210952560  

An automated preview of the documentation is available at [https://41.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://41.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Review 2 [Commented]

> Username: joaquintides  
> Created_at: 2025-08-22 07:23:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/41#pullrequestreview-3143420455  

📁 doc/bloom/tutorial.adoc

```diff
 342 |- Alternatively to the use of the embedded pretty-printer, you can explicitly
 342 |+ Alternatively, to the use of the embedded pretty-printer, you can explicitly
 343 | load the link:../../extra/boost_bloom_printers.py[`boost_bloom_printers.py`^]
```

> Username: joaquintides  
> Created_at: 2025-08-22 07:23:16 UTC  
> Updated_at: 2025-08-22 07:23:17 UTC  
> Url: https://github.com/boostorg/bloom/pull/41#discussion_r2292937975  

Thanks for your thorough proofreading! This correction proposal is incorrect, though. The intended meaning of the statement is  
  
"Instead of using the embedded pretty-printer, you can explicitly[...]",  
  
so the comma really changes the meaning. Care remove this correction so that I can merge the PR? Thank you!

> Username: ivanpanch  
> Created_at: 2025-08-22 07:32:14 UTC  
> Url: https://github.com/boostorg/bloom/pull/41#discussion_r2292959864  

Oh, sorry, now fixed!


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-08-22 07:40:56 UTC  
> Url: https://github.com/boostorg/bloom/pull/41#issuecomment-3213399540  

An automated preview of the documentation is available at [https://41.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://41.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-08-22 07:45:16 UTC  
> Url: https://github.com/boostorg/bloom/pull/41#issuecomment-3213411131  

## Welcome to [Codecov](https://codecov.io?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) :tada:  
  
Once you merge this PR into your default branch, you're all set! Codecov will compare coverage reports and display results in all future pull requests.  
  
Thanks for integrating Codecov - We've got you covered :open_umbrella:

---

## Comment 5

> Username: joaquintides  
> Created_at: 2025-08-22 07:59:54 UTC  
> Url: https://github.com/boostorg/bloom/pull/41#issuecomment-3213456105  

Merged, thanks for your contribution!

---
