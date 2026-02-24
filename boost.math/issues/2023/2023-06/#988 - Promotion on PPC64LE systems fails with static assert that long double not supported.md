# #988 - Promotion on PPC64LE systems fails with static assert that long double not supported [Closed]

> Username: mborland  
> Created at: 2023-06-01 07:54:07 UTC  
> Updated at: 2023-06-06 10:42:56 UTC  
> Closed at: 2023-06-06 10:42:56 UTC  
> Url: https://github.com/boostorg/math/issues/988  

x-ref: https://github.com/scipy/scipy/issues/18600  
  
PPC64LE uses ibm128 or float128 both of which should have sufficient precision for the special functions to work fine: https://developers.redhat.com/articles/2023/05/16/benefits-fedora-38-long-double-transition-ppc64le#.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-06-01 17:38:07 UTC  
> Url: https://github.com/boostorg/math/issues/988#issuecomment-1572512795  

I've commented on the linked SciPy issue, but I would be surprised if this works, we should certainly try and run some tests on that platform if we're going to do this.

---

## Comment 2

> Username: mborland  
> Created at: 2023-06-01 17:45:25 UTC  
> Url: https://github.com/boostorg/math/issues/988#issuecomment-1572521003  

@jzmaddock I figured you had a compelling reason, and was kind of surprised this popped up. @sdarwin was looking into a Docker solution for GHA. Turns out adding PPC64LE to drone would be have it running native, but be very expensive.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-06-01 17:54:20 UTC  
> Url: https://github.com/boostorg/math/issues/988#issuecomment-1572530818  

>Turns out adding PPC64LE to drone would be have it running native, but be very expensive.  
  
I think it's too niche then to worry about that much.  It's conceivable that if the right person at IBM was asked nicely (and no, I have no idea who that would be) they might donate a machine, but then someone would have to find space for it and pay for the electrons!

---

## Comment 4

> Username: mborland  
> Created at: 2023-06-06 10:42:50 UTC  
> Url: https://github.com/boostorg/math/issues/988#issuecomment-1578425488  

Closing for now as ibm128 is going away, and scipy is going to disable long doubles.
