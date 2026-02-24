# #39 - exception specification [Open]

> Username: vinniefalco  
> Created at: 2019-09-29 21:54:58 UTC  
> Updated at: 2021-07-21 17:16:11 UTC  
> Url: https://github.com/boostorg/docca/issues/39  

Would be nice to know about `noexcept`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-10 00:53:31 UTC  
> Url: https://github.com/boostorg/docca/issues/39#issuecomment-724377462  

Added to Doxygen in 1.8.16 https://github.com/doxygen/doxygen/commit/22a83d10db269c4a5d17873936292621eb64811f

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-11-10 00:54:13 UTC  
> Url: https://github.com/boostorg/docca/issues/39#issuecomment-724377717  

Before we switch to 1.8.16 we need to make sure it doesn't break Boost.Beast or Boost.JSON

---

## Comment 3

> Username: evanlenz  
> Created at: 2021-07-19 19:16:05 UTC  
> Url: https://github.com/boostorg/docca/issues/39#issuecomment-882794261  

It looks like I'm running Doxygen 1.8.15. Is that our standard? I wonder what additional enhancements (or fixes) might have been added to the XML output since then. I do see some activity in that area of the code, including this year.  
  
But as a next step for this issue, I can try testing the four docca uses that I know of (Beast, JSON, static_string, and docca example) with Doxygen 1.8.16 and make sure they produce the same (or better) HTML output.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-07-21 01:11:22 UTC  
> Url: https://github.com/boostorg/docca/issues/39#issuecomment-883809312  

I am also on 1.8.15

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-07-21 01:23:13 UTC  
> Url: https://github.com/boostorg/docca/issues/39#issuecomment-883813611  

Yeah lets see what happens with it

---

## Comment 6

> Username: evanlenz  
> Created at: 2021-07-21 17:16:11 UTC  
> Url: https://github.com/boostorg/docca/issues/39#issuecomment-884355002  

Welp, it looks like a number of things break (such as the link to the static_string reference page, just as one example I noticed). Next step is to look at the XML differences themselves. I also might try the very latest Doxygen version (1.9) to see if it diverges even further or if it somehow restores previous compatible behavior. For 1.8.16 it initially looks like a lot, but it might not be that much. I'll try to enumerate the issues. Main concern is if we make docca changes that aren't backward-compatible with 1.8.15. We'll see...
