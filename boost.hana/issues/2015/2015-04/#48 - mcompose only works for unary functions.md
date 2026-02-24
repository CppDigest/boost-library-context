# #48 - mcompose only works for unary functions [Closed]

> Username: ldionne  
> Created at: 2015-04-17 20:28:11 UTC  
> Updated at: 2015-04-19 19:58:04 UTC  
> Closed at: 2015-04-19 19:58:04 UTC  
> Url: https://github.com/boostorg/hana/issues/48  

The way `mcompose` is currently specified and implemented only works for unary functions. Calling `mcompose<...>(f, g)` with more than one argument is an error. We should probably do something similar to what's done in `compose`.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-04-19 17:14:37 UTC  
> Url: https://github.com/boostorg/hana/issues/48#issuecomment-94297807  

I think monadic composition does not generalize nicely to higher arities.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-04-19 19:58:04 UTC  
> Url: https://github.com/boostorg/hana/issues/48#issuecomment-94309110  

closed by fb518d859afd9fd4fd0e4a4b6c1fc826fd7ac1ae
