# #46 Add AppVeyor jobs for b2 variant=release [Merged]

> Username: mloskot  
> Created at: 2018-03-16 15:25:50 UTC  
> Updated at: 2018-03-30 20:27:47 UTC  
> Merged at: 2018-03-30 20:27:39 UTC  
> Closed at: 2018-03-30 20:27:39 UTC  
> Url: https://github.com/boostorg/gil/pull/46  

/cc @chhenning, @stefanseefeld yet another curious case :)  
  
For `variant-debug` all tests pass - this is the default AppVeyor builds variant for current the master.  
  
For `variant=release`, run for `test/image.cpp` will likely fail due to mismatch of CRC calculated `bgr121_basic_red_x` image.

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-03-28 21:02:23 UTC  
> Url: https://github.com/boostorg/gil/pull/46#issuecomment-377035418  

@stefanseefeld @chhenning I've cherry-picked the the UB fix commit cead84ee29c9b4eb904eaff337eba2a0b4672005 into this branch.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-03-28 21:20:33 UTC  
> Url: https://github.com/boostorg/gil/pull/46#issuecomment-377040868  

I've reverted 9d9fc09a8ffb39667bc27625285d2d8337ba4034 that temporarily disabled the failing checksum test cases and updated this PR to see if the UB fix also fixes the checksum bug #49

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-03-28 23:06:40 UTC  
> Url: https://github.com/boostorg/gil/pull/46#issuecomment-377067049  

I've copied the fix to more places, see #68

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-03-30 19:24:03 UTC  
> Url: https://github.com/boostorg/gil/pull/46#issuecomment-377602220  

Apparently, I have already committed `variant=release` for AppVeyor in e6808495f7d348026c07a00871f29b275738b227  
  
I think, I assumed the `b2` default variant is `release`, so I simply set it explicitly.  
  
I am going to update AppVeyor configuration to bring back `variant=debug` build jobs.

---
