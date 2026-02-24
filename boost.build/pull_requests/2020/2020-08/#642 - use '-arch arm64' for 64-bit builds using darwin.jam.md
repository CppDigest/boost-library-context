# #642 use '-arch arm64' for 64-bit builds using darwin.jam [Merged]

> Username: aumuell  
> Created at: 2020-08-06 12:45:45 UTC  
> Updated at: 2021-10-02 21:01:40 UTC  
> Merged at: 2020-08-29 05:02:46 UTC  
> Closed at: 2020-08-29 05:02:46 UTC  
> Url: https://github.com/boostorg/build/pull/642  

Even for 64-bit architectures, b2 would add '-arch arm' to the compiler  
options - but this is only valid for 32-bit arm builds.  
The problem was also observed here: https://stackoverflow.com/a/47096479

---

## Comment 1

> Username: michaelld  
> Created_at: 2020-08-27 16:00:37 UTC  
> Url: https://github.com/boostorg/build/pull/642#issuecomment-682039844  

This change is a good place to start for adding in ARM64 support to Boost. It covers the non-universal build case, which is what most users will need. It does not handle universal installs -- x86_64 + ARM64 -- which will require some added code and logic since Apple is introducing a new architecture & the current darwin.jam logic is not robust enough to directly handle it.

---

## Comment 2

> Username: aumuell  
> Created_at: 2020-08-29 11:40:47 UTC  
> Url: https://github.com/boostorg/build/pull/642#issuecomment-683278966  

Thank you!

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:39 UTC  
> Url: https://github.com/boostorg/build/pull/642#issuecomment-932819942  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
