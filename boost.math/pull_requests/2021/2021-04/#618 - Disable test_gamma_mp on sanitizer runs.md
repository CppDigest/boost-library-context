# #618 Disable test_gamma_mp on sanitizer runs [Merged]

> Username: mborland  
> Created at: 2021-04-27 17:58:14 UTC  
> Updated at: 2021-05-02 12:11:12 UTC  
> Merged at: 2021-05-02 11:16:22 UTC  
> Closed at: 2021-05-02 11:16:23 UTC  
> Url: https://github.com/boostorg/math/pull/618  

Sporadically the Drone [USAN test fails](https://drone.cpp.al/boostorg/math/183/2/2) at `test_gamma_mp` for out of memory error. This disables `test_gamma_mp` on sanitizer runs.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-04-27 18:42:40 UTC  
> Updated_at: 2021-04-27 18:44:37 UTC  
> Url: https://github.com/boostorg/math/pull/618#issuecomment-827832822  

>  out of memory error  
  
It appears as though the test allocates 5GB memory? That seems like a lot of memory. Maybe a two dimensional storage of MP types? I could try to take a look if I ever get a free _gamma moment_.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-05-02 10:05:13 UTC  
> Url: https://github.com/boostorg/math/pull/618#issuecomment-830782963  

@jzmaddock This should be good pending review. The CI failures are on Github's end.

---
