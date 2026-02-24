# #73 - [Metafunction] Reconsider auto decltype_ [Closed]

> Username: ldionne  
> Created at: 2015-05-09 19:36:00 UTC  
> Updated at: 2016-01-30 16:41:24 UTC  
> Closed at: 2016-01-30 16:41:24 UTC  
> Url: https://github.com/boostorg/hana/issues/73  

While I'm OK with `sizeof_` and `alignof_` accepting arbitrary objects (because they do so in the language), I'm growing less and less certain whether it is really desirable to have all `Metafunction`s work  on arbitrary objects. I think  one should use `decltype_` explicitly for these cases.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-01-30 16:41:24 UTC  
> Url: https://github.com/boostorg/hana/issues/73#issuecomment-177238154  

The resolution of this issue will be the same as the resolution of  PR #249. Hence, I'm closing this in favour of #249.
