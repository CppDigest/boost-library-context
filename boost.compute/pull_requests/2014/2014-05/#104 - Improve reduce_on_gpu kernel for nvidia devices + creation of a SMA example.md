# #104 Improve reduce_on_gpu kernel for nvidia devices + creation of a SMA example [Closed]

> Username: banche  
> Created at: 2014-05-06 16:22:23 UTC  
> Updated at: 2014-07-20 11:44:24 UTC  
> Closed at: 2014-05-07 00:19:34 UTC  
> Url: https://github.com/boostorg/compute/pull/104  

For nvidia device, the reduction operation inside a warp can be optimized. The optimization is valid for all nvidia's device with warpsize >= 32 (actually they all have warpsize == 32 right now)  
the speed is increased by 5 to 10% with that optimization.  
  
I also added a simple moving average example. Can be improved using the KahanSum operator for more precision during the scan (or using double if it's supported)

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-05-06 17:02:09 UTC  
> Url: https://github.com/boostorg/compute/pull/104#issuecomment-42329541  

[![Coverage Status](https://coveralls.io/builds/743541/badge)](https://coveralls.io/builds/743541)  
  
Coverage remained the same when pulling **e0aaf6d253e1995b292dfee352c8e0d14112c055 on banche:develop** into **d23894322f28573115fbf8aa17355610c348208a on kylelutz:develop**.

---

## Comment 2

> Username: banche  
> Created_at: 2014-05-07 00:19:34 UTC  
> Url: https://github.com/boostorg/compute/pull/104#issuecomment-42375906  

indeed, my bad! Corrected on my branch develop

---
