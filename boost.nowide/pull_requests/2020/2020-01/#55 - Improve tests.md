# #55 Improve tests [Merged]

> Username: Flamefire  
> Created at: 2020-01-01 17:19:16 UTC  
> Updated at: 2020-01-05 11:38:26 UTC  
> Merged at: 2020-01-05 11:38:23 UTC  
> Closed at: 2020-01-05 11:38:23 UTC  
> Url: https://github.com/boostorg/nowide/pull/55  

Cherry pick commits from my fork improving the tests in various areas and fix detected issues  
  
Summary:  
- Split tests into files and functions  
- Add missing test cases found by cursory review  
- fix fstream and filebuf issues (wrong default arguments, wrong behavior, to many flushes, ...)  
- add test for custom filebuf/filestream on non-windows  
- make stackstring conserve NULL input

---

## Comment 1

> Username: Flamefire  
> Created_at: 2020-01-02 21:06:28 UTC  
> Url: https://github.com/boostorg/nowide/pull/55#issuecomment-570349157  

@pdimov If you want to have a look first tell me (I'd suggest to look at the commits not the PR change), otherwise I'll merge tomorrow.

---
