# #256 - [sort] Investigate the performance of hana::sort [Open]

> Username: ldionne  
> Created at: 2016-02-28 14:03:14 UTC  
> Updated at: 2016-05-27 16:55:33 UTC  
> Url: https://github.com/boostorg/hana/issues/256  

As outlined in https://github.com/edouarda/brigand/issues/118#issuecomment-189834748, `hana::sort` seems to perform best in the case of a reversed list, which is the only benchmark that we have. We should investigate this and probably change the benchmark.

---

## Comment 1

> Username: odinthenerd  
> Created at: 2016-02-28 20:22:04 UTC  
> Url: https://github.com/boostorg/hana/issues/256#issuecomment-189940835  

is there anywhere I can pull your hana adaption of the brigand sort from?

---

## Comment 2

> Username: ldionne  
> Created at: 2016-02-28 23:11:43 UTC  
> Url: https://github.com/boostorg/hana/issues/256#issuecomment-189965590  

It's extremely dirty: 96ab5a2b603bf9fa56f72f082a2efb2c08fd8327
