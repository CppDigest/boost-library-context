# #681 - Should we do a module? [Open]

> Username: NAThompson  
> Created at: 2021-08-30 03:16:40 UTC  
> Updated at: 2021-11-28 12:10:43 UTC  
> Url: https://github.com/boostorg/math/issues/681  

Compiler support is coming along, and we have a standalone which I assume is half the work.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-08-30 09:24:12 UTC  
> Url: https://github.com/boostorg/math/issues/681#issuecomment-908187979  

When I got regex standalone I started experimenting with this and msvc and concluded the support wasn't *quite* there yet.  Have things changed?  
  
But yes by all means lets experiment with this, maybe something near the core like the constants would be a place to start?

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-08-30 14:14:48 UTC  
> Url: https://github.com/boostorg/math/issues/681#issuecomment-908380131  

[This page](https://en.cppreference.com/w/cpp/compiler_support) lists MSVC as fully green right now on module support. I *hope* this means you tried this a few months ago? Otherwise you're probably right . . .

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-08-31 08:14:32 UTC  
> Url: https://github.com/boostorg/math/issues/681#issuecomment-909005981  

Yes it was a good 6 months ago and things are changing fast.  
  
I would tend to favour full module support as in:  
  
```  
import boost.math.constants ;  
```  
  
But that means quite a bit of braindead internal reorganisation I suspect.

---

## Comment 4

> Username: mborland  
> Created at: 2021-09-05 17:56:16 UTC  
> Url: https://github.com/boostorg/math/issues/681#issuecomment-913198500  

@NAThompson A fairly easy test bed could be ccmath; it is almost entirely self-contained (only math dependency is <boost/math/tools/is_constant_evaluated.hpp>). I haven't really read into implementing modules yet, but I can pursue it if we think it's time.

---

## Comment 5

> Username: NAThompson  
> Created at: 2021-09-06 15:39:41 UTC  
> Url: https://github.com/boostorg/math/issues/681#issuecomment-913734622  

Ok, looks like clang, gcc and MSVC at least have partial support. Not something to agonize over if we experience pain, but might be fun to try out and see where it goes.

---

## Comment 6

> Username: mborland  
> Created at: 2021-11-28 12:10:43 UTC  
> Url: https://github.com/boostorg/math/issues/681#issuecomment-981074330  

See #723
