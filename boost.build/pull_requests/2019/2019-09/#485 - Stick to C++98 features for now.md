# #485 Stick to C++98 features for now [Closed]

> Username: tanzislam  
> Created at: 2019-09-22 15:18:53 UTC  
> Updated at: 2021-10-02 21:01:23 UTC  
> Closed at: 2020-11-27 00:14:28 UTC  
> Url: https://github.com/boostorg/build/pull/485  

Fixes #473, #534, #535. Tested with the "borland" toolchain, which still uses `bcc32.exe`.

---

## Review 1 [Approved]

> Username: vahtis  
> Created_at: 2020-01-29 10:18:56 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/build/pull/485#pullrequestreview-349996529  

Seems to correct Sun Studio 12.6 too.

---

## Comment 2

> Username: tanzislam  
> Created_at: 2020-11-27 00:14:28 UTC  
> Updated_at: 2020-12-03 13:27:01 UTC  
> Url: https://github.com/boostorg/build/pull/485#issuecomment-734513409  

"master" branch is making more use of C++11 features by now. I'll update the build to use `bcc32c.exe` instead: #680.

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:22 UTC  
> Url: https://github.com/boostorg/build/pull/485#issuecomment-932819907  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
