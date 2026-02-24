# #572 Replace `/Ox` with `/O2` and remove `/Zi` [Merged]

> Username: Kojoley  
> Created at: 2020-04-19 20:31:11 UTC  
> Updated at: 2021-10-02 21:02:17 UTC  
> Merged at: 2020-04-19 22:17:40 UTC  
> Closed at: 2020-04-19 22:17:40 UTC  
> Url: https://github.com/boostorg/build/pull/572  

Reduces b2 executable size for 60% (1174KiB vs 465KiB)  
  
`/O2` is essentially `/Ox /GF /Gy`  
`/Zi` implies `/debug` which disables `/opt` optimizations  
  
Ping me if you prefer to add `/opt:ref,icf,lbr` instead of removing `/Zi`

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2020-04-19 22:18:25 UTC  
> Url: https://github.com/boostorg/build/pull/572#issuecomment-616234312  

This is great. Thanks.

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:02:16 UTC  
> Url: https://github.com/boostorg/build/pull/572#issuecomment-932820048  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
