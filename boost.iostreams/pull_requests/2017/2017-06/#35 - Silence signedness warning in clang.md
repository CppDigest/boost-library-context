# #35 Silence signedness warning in clang [Merged]

> Username: rcombs  
> Created at: 2017-06-07 22:53:01 UTC  
> Updated at: 2017-06-27 17:53:54 UTC  
> Merged at: 2017-06-27 17:47:34 UTC  
> Closed at: 2017-06-27 17:47:34 UTC  
> Url: https://github.com/boostorg/iostreams/pull/35  

`warning: implicit conversion from 'const int' to 'char' changes value from 139 to -117 [-Wconstant-conversion]`

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-06-27 17:53:54 UTC  
> Url: https://github.com/boostorg/iostreams/pull/35#issuecomment-311435520  

I erroneously committed this change and then backed it out. All PR changes must be done on the 'develop' branch and not on the 'master' branch. If you would like to redo this change fro the 'develop' branch I will commit it and then, if all tests pass, I can merge it to the 'master' branch again.

---
