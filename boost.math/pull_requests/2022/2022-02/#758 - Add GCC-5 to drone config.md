# #758 Add GCC-5 to drone config [Merged]

> Username: mborland  
> Created at: 2022-02-09 11:04:28 UTC  
> Updated at: 2022-02-10 14:50:24 UTC  
> Merged at: 2022-02-10 12:57:45 UTC  
> Closed at: 2022-02-10 12:57:45 UTC  
> Url: https://github.com/boostorg/math/pull/758  

Based off conversation in https://github.com/boostorg/boost/issues/610 add GCC-5 back to our testing.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-02-09 12:37:08 UTC  
> Url: https://github.com/boostorg/math/pull/758#issuecomment-1033716271  

@jzmaddock The [drone run is clean](https://drone.cpp.al/boostorg/math/591) with GCC-5. Is this a change you want to pursue?  
  
CC: @ckormanyos

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-02-10 12:44:47 UTC  
> Updated_at: 2022-02-10 12:45:37 UTC  
> Url: https://github.com/boostorg/math/pull/758#issuecomment-1034879663  

> with GCC-5  
  
We never really formalized plans here. But for clarity's sake,...  
  
- I would like to try for GCC-5 on both Math and Multiprecision for 1.79.  
- In the summer, we can see how things look in CI for runners on 4.8 or 4.9.  
  
If it 4.x (on `-std=c++11` only) goes well, we can include these in 1.80 as well. If not, then simply bail on that legacy 4.x support. We might have to change some of the PP logic to go back down to 4. or we might decide to let it go unsuccessfully if the work is not warranted by the gains.

---

## Comment 3

> Username: mborland  
> Created_at: 2022-02-10 12:57:11 UTC  
> Url: https://github.com/boostorg/math/pull/758#issuecomment-1034891022  

I can merge this in then; GCC-5 didn't encounter any issues on the drone run. GCC-5 was released in April 2015 so if 4.X does not work out of the box I don't think I would sink much time into making it work. Having a less than 7 year old compiler for an 11 year old standard does not seem like a big ask.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-02-10 14:50:24 UTC  
> Url: https://github.com/boostorg/math/pull/758#issuecomment-1035010704  

> I can merge this in then; GCC-5 didn't encounter any issues  
  
Great. Thank you Matt (@mborland)  
  
We can now for 1.79 with fairness and clarity document Math and Multiprecision to go back to GCC-5.

---
