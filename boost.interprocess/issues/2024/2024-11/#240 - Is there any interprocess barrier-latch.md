# #240 - Is there any interprocess barrier/latch? [Closed]

> Username: LXYan2333  
> Created at: 2024-11-11 12:15:54 UTC  
> Updated at: 2026-01-07 08:44:07 UTC  
> Closed at: 2026-01-07 08:44:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/240  

Hello:  
  
thanks for this awesome library. I just wonder, is there any interprocess latch or barrier to sync several processes? I found some [old document](https://www.boost.org/doc/libs/1_38_0/boost/interprocess/sync/interprocess_barrier.hpp) but they are not in current version of `boost::interprocess`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 08:44:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/240#issuecomment-3717857996  

Reviewing old bugs. Thanks for your kind workds. No, sorry they were removed because they were buggy and not working properly, there is no plan to bring them back.
