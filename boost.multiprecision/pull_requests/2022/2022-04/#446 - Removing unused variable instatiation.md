# #446 Removing unused variable instatiation [Merged]

> Username: cdesouza-chromium  
> Created at: 2022-04-06 16:34:40 UTC  
> Updated at: 2022-04-30 13:28:41 UTC  
> Merged at: 2022-04-30 13:27:44 UTC  
> Closed at: 2022-04-30 13:27:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/446  

The lines removed have no observable effect beyond instatiating the  
variable and incremeting it.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-04-06 16:54:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/446#issuecomment-1090490355  

That's alarming, I'll double check there isn't a wider bug than just stray code left behind here.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-04-30 09:43:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/446#issuecomment-1113958852  

Hi @cdesouza-chromium if I understand, you simply remove a few lines of unused code in this PR. Is this correct?  
  
Hi John (@jzmaddock) should we merge this PR (after yours cycles) also before going further with other warning-resolutions?

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-04-30 13:28:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/446#issuecomment-1113989146  

> we merge this PR (after yours cycles)   
  
Done.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-04-30 13:28:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/446#issuecomment-1113989257  

Thank you @cdesouza-chromium for finding this hard-to-find warning-artifact.

---
