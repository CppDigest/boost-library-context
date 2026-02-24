# #1078 - http-crawl example hangs [Open]

> Username: vinniefalco  
> Created at: 2018-03-25 13:54:16 UTC  
> Updated at: 2018-05-21 18:01:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1078  

The http-crawl example visits thousands of websites using a pool of threads each with their own `io_context`, but some of the sessions hang and the program doesn't terminate.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-21 16:16:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1078#issuecomment-383309856  

This is very likely related: https://github.com/boostorg/beast/issues/1100

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-05-21 17:47:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1078#issuecomment-390729757  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-21 18:01:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1078#issuecomment-390733703  

Not resolved.
