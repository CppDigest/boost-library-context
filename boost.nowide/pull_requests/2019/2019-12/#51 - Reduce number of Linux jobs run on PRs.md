# #51 Reduce number of Linux jobs run on PRs [Merged]

> Username: Flamefire  
> Created at: 2019-12-15 17:03:54 UTC  
> Updated at: 2019-12-19 08:23:01 UTC  
> Merged at: 2019-12-19 08:22:58 UTC  
> Closed at: 2019-12-19 08:22:58 UTC  
> Url: https://github.com/boostorg/nowide/pull/51  

The library is mostly for windows, so there isn't much to test on Linux  
Reduce to oldest and most recent compilers  
But still test all on pushes to eventually detect compiler version specific issues  
  
Fixes #50

---

## Comment 1

> Username: Flamefire  
> Created_at: 2019-12-18 17:23:14 UTC  
> Url: https://github.com/boostorg/nowide/pull/51#issuecomment-567130064  

@pdimov Can you have a look? My intentions are described in #50, basically:  
- test PRs with oldest and newest compiler to catch pretty much everything  
- test pushes to develop/master with everything to still catch mid-version regressions etc   
  
And you might want to use https://github.com/boostorg/nowide/pull/51/commits/ddcbf8ab6dd383ac7086e9b1e4f71b52a3605b33 as a template and/or for the template repo. No functional changes, only simplifications to avoid repetitions and lengthy files

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-12-18 17:38:54 UTC  
> Url: https://github.com/boostorg/nowide/pull/51#issuecomment-567135784  

Looks good to me.

---
