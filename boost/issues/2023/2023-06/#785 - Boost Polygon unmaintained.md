# #785 - Boost Polygon unmaintained [Open]

> Username: fiesh  
> Created at: 2023-06-15 09:25:27 UTC  
> Updated at: 2023-06-15 09:33:26 UTC  
> Url: https://github.com/boostorg/boost/issues/785  

Hi,  
  
The Boost Polygon library seems unmaintained.  [https://github.com/boostorg/polygon/pull/74](This fix) should quite clearly not introduce a regression and fixes an essential bug in the library.  
  
It would be great if someone could take over and at least include basic fixes.  There's also tons of garbage unused functions in Boost Polygon (for example `testTrapezoidArbitraryFormationSelfTouch1`) that write to `stdout` and should probably be unit tests somewhere, but in the current form make no sense and only slow down compilation.  Removing all this cruft would probably also be a great addition.  
  
Thanks!

---

## Comment 1

> Username: fiesh  
> Created at: 2023-06-15 09:33:25 UTC  
> Url: https://github.com/boostorg/boost/issues/785#issuecomment-1592703555  

I was wrong, this function actually *is* used in the library's tests... it still seems horrible that they are included in the actual distribution, but I guess separating that properly would be beyond the scope of simple fixes.  I don't understand how this design passed reviews, but refactoring this is clearly beyond the scope of simple maintenance. So please ignore my cruft-removal request.
