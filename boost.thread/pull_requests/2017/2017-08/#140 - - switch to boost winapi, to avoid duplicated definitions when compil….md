# #140 - switch to boost winapi, to avoid duplicated definitions when compil… [Merged]

> Username: SSE4  
> Created at: 2017-08-31 07:55:58 UTC  
> Updated at: 2017-09-16 17:43:16 UTC  
> Merged at: 2017-09-06 22:56:11 UTC  
> Closed at: 2017-09-06 22:56:11 UTC  
> Url: https://github.com/boostorg/thread/pull/140  

…ing using Clang on Windows  
  
#139

---

## Comment 1

> Username: viboes  
> Created_at: 2017-08-31 16:45:31 UTC  
> Url: https://github.com/boostorg/thread/pull/140#issuecomment-326354961  

Andrey, could you check this PR using WinApi?

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-09-05 17:36:53 UTC  
> Url: https://github.com/boostorg/thread/pull/140#issuecomment-327249746  

Sorry, @viboes, I didn't receive a notification. I did a quick check and there is an include missing. I'm running the checks again now and will create a PR to this PR to fix it.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-09-05 20:06:27 UTC  
> Url: https://github.com/boostorg/thread/pull/140#issuecomment-327288088  

Here's the PR with my fixes: https://github.com/SSE4/thread/pull/1.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-09-06 16:52:50 UTC  
> Url: https://github.com/boostorg/thread/pull/140#issuecomment-327546404  

Andrey, if I understand you approve this PR.

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-09-06 17:04:06 UTC  
> Url: https://github.com/boostorg/thread/pull/140#issuecomment-327549506  

My limited testing did not reveal errors and I'm generally in favor of porting to Boost.WinAPI, so you could say so. IMHO, you can merge it rather safely and then watch the test results.

---

## Comment 6

> Username: viboes  
> Created_at: 2017-09-06 22:55:38 UTC  
> Url: https://github.com/boostorg/thread/pull/140#issuecomment-327634161  

I'm confident on your testing.

---

## Comment 7

> Username: viboes  
> Created_at: 2017-09-16 16:52:09 UTC  
> Url: https://github.com/boostorg/thread/pull/140#issuecomment-329980500  

There is an additional issue on Windows that maybe it can be managed by the move to WinAPI.  
  
https://svn.boost.org/trac10/ticket/12323  
  
Please, could you take a look?

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-09-16 17:43:16 UTC  
> Url: https://github.com/boostorg/thread/pull/140#issuecomment-329984058  

That issue is unrelated to the move. AFAICS, that piece of code unconditionally includes windows.h, I see no reason to change that. I'll create a separate PR for that ticket.

---
