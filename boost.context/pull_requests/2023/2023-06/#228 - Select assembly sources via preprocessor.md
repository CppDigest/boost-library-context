# #228 Select assembly sources via preprocessor [Merged]

> Username: Kojoley  
> Created at: 2023-06-14 02:28:08 UTC  
> Updated at: 2024-03-24 02:04:26 UTC  
> Merged at: 2023-07-03 08:36:20 UTC  
> Closed at: 2023-07-03 08:36:21 UTC  
> Url: https://github.com/boostorg/context/pull/228  

* Fixes cross-compilation abi and binformat detection.  
* `abi` and `binary-format` are now optional but can be used to override autodetection.

---

## Comment 1

> Username: olk  
> Created_at: 2023-06-14 07:56:04 UTC  
> Url: https://github.com/boostorg/context/pull/228#issuecomment-1590669060  

I don't want a big commit containing changes for many different issues - please create one commit for one fix.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2023-06-14 13:43:09 UTC  
> Url: https://github.com/boostorg/context/pull/228#issuecomment-1591236881  

> I don't want a big commit containing changes for many different issues - please create one commit for one fix.  
  
You mean pull request instead of commit? Because currently there is one commit for one thing and I have probably even split it too much.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2023-06-14 13:59:59 UTC  
> Url: https://github.com/boostorg/context/pull/228#issuecomment-1591269182  

I've removed CI changes from the PR, some of them I've submitted as separate PRs.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2023-06-21 02:19:42 UTC  
> Url: https://github.com/boostorg/context/pull/228#issuecomment-1599971817  

Ping

---

## Comment 5

> Username: olk  
> Created_at: 2023-07-03 08:36:25 UTC  
> Url: https://github.com/boostorg/context/pull/228#issuecomment-1617631867  

ty

---

## Comment 6

> Username: olk  
> Created_at: 2023-07-22 15:29:58 UTC  
> Url: https://github.com/boostorg/context/pull/228#issuecomment-1646607535  

I was forced to revert all you commits because the changes prevented the release (errors on selecting the correct source etc.)

---

## Comment 7

> Username: Kojoley  
> Created_at: 2024-03-24 02:04:24 UTC  
> Url: https://github.com/boostorg/context/pull/228#issuecomment-2016664760  

> I was forced to revert all you commits because the changes prevented the release (errors on selecting the correct source etc.)  
  
It was b2 bug (https://github.com/bfgroup/b2/pull/326), do you interested in trying this again?

---
