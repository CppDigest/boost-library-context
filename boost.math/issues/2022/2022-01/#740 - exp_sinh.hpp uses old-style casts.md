# #740 - exp_sinh.hpp uses old-style casts [Closed]

> Username: rnburn  
> Created at: 2022-01-14 20:42:07 UTC  
> Updated at: 2022-01-18 02:24:24 UTC  
> Closed at: 2022-01-18 02:24:23 UTC  
> Url: https://github.com/boostorg/math/issues/740  

boost quadrature errors with the message below when compiled with the flags `-Werror,-Wold-style-cast`  
  
It's easy enough to work around but there shouldn't be any need to use old-style casts.  
  
```  
external/boost/boost/math/quadrature/exp_sinh.hpp:64:18: error: use of old-style cast [-Werror,-Wold-style-cast]  
        if (a == (Real) 0)  
                 ^      ~  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-01-14 21:06:39 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1013473556  

Yup, I'll fix it.

---

## Comment 2

> Username: rnburn  
> Created at: 2022-01-14 21:07:59 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1013474226  

Thanks!   
  
Btw looking through the code, I see a few other places where it's doing old-style casts:  
https://github.com/boostorg/math/blob/develop/include/boost/math/quadrature/detail/exp_sinh_detail.hpp#L225

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-01-14 21:08:25 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1013474474  

I'll fix 'em all!

---

## Comment 4

> Username: NAThompson  
> Created at: 2022-01-15 00:31:06 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1013559932  

@rnburn : Should be fixed in 741. Once the CI cycles I'll merge; are you consuming the standalone? If so you can consume this independently of the rest of boost.

---

## Comment 5

> Username: rnburn  
> Created at: 2022-01-15 00:47:42 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1013563850  

Great! Thanks for the the quick turnaround.  
  
I was using it through https://github.com/nelhage/rules_boost, but I'm sure I'll be able to find a way to adopt the fix.

---

## Comment 6

> Username: NAThompson  
> Created at: 2022-01-15 16:19:55 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1013710400  

@rnburn : Fix is merged.  
  
Leaving this issue open as I made the same mistake in sinh-sinh and tanh-sinh quadrature, so I'll close once I get those done.

---

## Comment 7

> Username: NAThompson  
> Created at: 2022-01-15 17:32:35 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1013721724  

@jzmaddock : Will I get you into merge hell if I make those diffs? I see you have a pretty large outstanding diff to these files.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2022-01-16 09:16:36 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1013839015  

I've just merged one lot of develop changes, if you don't mind holding off until CI has cycled on https://github.com/boostorg/math/pull/722 I'll merge all that to develop if it's clean now.  If not clean, just go ahead and I'll merge and resolve the conflicts on the branch, the merge conflicts aren't that awful.

---

## Comment 9

> Username: NAThompson  
> Created at: 2022-01-18 02:24:23 UTC  
> Url: https://github.com/boostorg/math/issues/740#issuecomment-1015014935  

Just opened a PR which completely removes all old-style casts in all the double-exponential quadratures. Closing . . .
