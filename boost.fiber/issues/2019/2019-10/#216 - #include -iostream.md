# #216 - #include <iostream> [Closed]

> Username: dixlorenz  
> Created at: 2019-10-27 10:38:29 UTC  
> Updated at: 2020-01-13 07:29:08 UTC  
> Closed at: 2020-01-13 07:29:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/216  

I just noticed context.hpp includes <iostream>. Wouldn't <iosfwd> be enough? Or some other solution? context.hpp is practically used by ever other header file, it permeates the whole build and really, fibers have nothing to do with IO, it shouldn't incur that cost.

---

## Comment 1

> Username: dixlorenz  
> Created at: 2019-10-29 23:18:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/216#issuecomment-547671301  

grumble. Something stripped out the header names :-) it does include "iostream" and I think "iosfwd" should be enough...

---

## Comment 2

> Username: olk  
> Created at: 2020-01-13 07:29:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/216#issuecomment-573538878  

fiber::id has an shift op. for output
