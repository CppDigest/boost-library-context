# #504 - const decoded from const encoded [Closed]

> Username: vinniefalco  
> Created at: 2022-09-05 15:10:24 UTC  
> Updated at: 2022-09-14 00:33:02 UTC  
> Closed at: 2022-09-14 00:33:02 UTC  
> Url: https://github.com/boostorg/url/issues/504  

params, segments container views returned from parsing non-url strings need a `decoded` function which returns the a container returning decoded strings, referencing the same non-url character buffer.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-08 21:22:08 UTC  
> Url: https://github.com/boostorg/url/issues/504#issuecomment-1241247200  

Should be done for segments, and the interface is via conversion operator not `decoded()` function.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-14 00:33:02 UTC  
> Url: https://github.com/boostorg/url/issues/504#issuecomment-1246087815  

Should be all done now, and there are tests.
