# #571 Set CRLF line endings for .bat/.cmd files [Merged]

> Username: Kojoley  
> Created at: 2020-04-18 21:04:45 UTC  
> Updated at: 2021-10-02 21:02:04 UTC  
> Merged at: 2020-05-11 00:39:25 UTC  
> Closed at: 2020-05-11 00:39:25 UTC  
> Url: https://github.com/boostorg/build/pull/571  

Git does not understand `svneol`  
  
Fixes #477

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-04-18 22:10:49 UTC  
> Url: https://github.com/boostorg/build/pull/571#issuecomment-615960593  

Hmm, I am not sure why CI still fails, `git clone https://github.com/Kojoley/build.git -b feature/crlf-eol-for-bat-cmd` clones with CRLF

---

## Comment 2

> Username: Kojoley  
> Created_at: 2020-04-18 22:13:10 UTC  
> Url: https://github.com/boostorg/build/pull/571#issuecomment-615961044  

I think it is better to merge this anyway because locally it solves the problem for me

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:02:03 UTC  
> Url: https://github.com/boostorg/build/pull/571#issuecomment-932820020  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
