# #848 - Strip the example executables for release [Closed]

> Username: vinniefalco  
> Created at: 2017-10-28 17:39:45 UTC  
> Updated at: 2018-05-01 17:32:43 UTC  
> Closed at: 2018-05-01 17:32:43 UTC  
> Url: https://github.com/boostorg/beast/issues/848  

For release builds on CI, the examples should be built in a way that produces the smallest possible executable. Furthermore, the build should log the sizes of those executables.

---

## Comment 1

> Username: djarek  
> Created at: 2017-10-28 17:41:38 UTC  
> Url: https://github.com/boostorg/beast/issues/848#issuecomment-340207825  

Usefull g++ flags to consider:  
```-flto -s -Os```

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:46 UTC  
> Url: https://github.com/boostorg/beast/issues/848#issuecomment-384003629  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-05-01 17:32:35 UTC  
> Url: https://github.com/boostorg/beast/issues/848#issuecomment-385733410  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
