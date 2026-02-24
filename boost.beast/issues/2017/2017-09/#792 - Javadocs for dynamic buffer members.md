# #792 - Javadocs for dynamic buffer members [Closed]

> Username: vinniefalco  
> Created at: 2017-09-21 19:34:28 UTC  
> Updated at: 2019-01-02 02:17:12 UTC  
> Closed at: 2019-01-02 02:17:12 UTC  
> Url: https://github.com/boostorg/beast/issues/792  

All dynamic buffer implementation member function javadocs should repeat the information in the DynamicBuffer requirements. Specifically that calls to `prepare` and `consume` will invalidate all buffer sequences returned by `data` and `prepare`.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:50 UTC  
> Url: https://github.com/boostorg/beast/issues/792#issuecomment-384003657  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-01-02 02:17:12 UTC  
> Url: https://github.com/boostorg/beast/issues/792#issuecomment-450776557  

Done
