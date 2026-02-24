# #251 No multiprecision depend [Merged]

> Username: jzmaddock  
> Created at: 2019-09-12 08:02:29 UTC  
> Updated at: 2019-10-17 15:15:29 UTC  
> Merged at: 2019-09-14 16:03:23 UTC  
> Closed at: 2019-09-14 16:03:23 UTC  
> Url: https://github.com/boostorg/math/pull/251  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-09-12 08:03:33 UTC  
> Url: https://github.com/boostorg/math/pull/251#issuecomment-530713935  

@NAThompson : can you double check that this is all OK for you?  It removes the dependency from Math to Multiprecision, and also simplifies quadratic_roots.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-09-12 11:07:28 UTC  
> Url: https://github.com/boostorg/math/pull/251#issuecomment-530776857  

There's a further issue here that I don't know what to do about: if the platform has no fma at all (embedded platforms?) then even if the user isn't using quadratic_roots they'll still get a hard error when they include roots.hpp.  But we might just have to push that into the long grass for now, as I know of no solution other than to apply workarounds on a platform by platform basis.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-09-12 12:50:07 UTC  
> Url: https://github.com/boostorg/math/pull/251#issuecomment-530809823  

> There's a further issue here that I don't know what to do about: if the platform has no fma at all (embedded platforms?) then even if the user isn't using quadratic_roots they'll still get a hard error when they include roots.hpp.  
  
`std::fma` resorts to emulation when the `fma` instruction isn't available on the target. As to your change, I'm still fine with it, because if there is no emulation implemented and there is no hardware instruction, then there's really no advantage in using the boost version of quadratic roots.  
  
As to removing the dependency on multiprecision: I'm amazed that you managed to work your magic to solve that problem!

---
