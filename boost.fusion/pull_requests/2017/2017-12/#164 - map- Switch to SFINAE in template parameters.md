# #164 map: Switch to SFINAE in template parameters [Merged]

> Username: Kojoley  
> Created at: 2017-12-13 20:40:35 UTC  
> Updated at: 2017-12-14 01:58:26 UTC  
> Merged at: 2017-12-13 22:45:28 UTC  
> Closed at: 2017-12-13 22:45:28 UTC  
> Url: https://github.com/boostorg/fusion/pull/164  

Fixes #163.  
  
Since C++11 we can do SFINAE in template parameters. VS2017 15.5 doesn't like  
the SFINAE on argument here, but do not ask me why, because I have no idea.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-13 22:32:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/164#issuecomment-351546734  

The issue is Fusion has always supported C++03. Do we stop supporting legacy code now?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-12-13 22:35:10 UTC  
> Url: https://github.com/boostorg/fusion/pull/164#issuecomment-351547286  

It is C++11 interface https://github.com/boostorg/fusion/blob/099333b61d800af122662677c95d3d04759b5a9a/include/boost/fusion/container/map/map.hpp#L14-L23

---

## Comment 3

> Username: djowel  
> Created_at: 2017-12-13 22:44:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/164#issuecomment-351549526  

Ah, right! I missed that! Sorry :P

---

## Comment 4

> Username: Flast  
> Created_at: 2017-12-14 00:38:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/164#issuecomment-351571414  

@djowel please stay until passing CIs.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-12-14 00:42:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/164#issuecomment-351572097  

I thought the same.

---

## Comment 6

> Username: djowel  
> Created_at: 2017-12-14 01:58:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/164#issuecomment-351584611  

Sorry, I didn't realize that. I guess it's too late at this point?

---
