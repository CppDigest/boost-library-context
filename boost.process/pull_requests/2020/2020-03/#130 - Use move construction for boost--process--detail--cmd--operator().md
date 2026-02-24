# #130 Use move construction for boost::process::detail::cmd::operator() [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 05:05:10 UTC  
> Updated at: 2021-02-18 03:24:19 UTC  
> Closed at: 2020-05-20 13:40:37 UTC  
> Url: https://github.com/boostorg/process/pull/130  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 13:40:37 UTC  
> Url: https://github.com/boostorg/process/pull/130#issuecomment-631480482  

That actually introduces an additional copy.

---

## Comment 2

> Username: jonesmz  
> Created_at: 2020-05-20 16:14:27 UTC  
> Url: https://github.com/boostorg/process/pull/130#issuecomment-631576103  

Elaborate please?  
  
I don't see how this could introduce an additional copy. At worse its the same number.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2020-05-20 16:52:32 UTC  
> Url: https://github.com/boostorg/process/pull/130#issuecomment-631597472  

Ah you're right. You have one copy and one move or two moves. Currently you have one of either, plus passing a reference.

---

## Comment 4

> Username: jonesmz  
> Created_at: 2021-02-18 03:24:17 UTC  
> Url: https://github.com/boostorg/process/pull/130#issuecomment-781019395  

I'm deleting this branch. You take too long to do anything, and you close PRs for reasons that you later admit are wrong, but then leave the PR closed instead of doing anything about being wrong.

---
