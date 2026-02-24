# #46 Minor cleanup [Merged]

> Username: Flamefire  
> Created at: 2019-12-13 17:59:19 UTC  
> Updated at: 2019-12-14 15:47:53 UTC  
> Merged at: 2019-12-14 15:45:31 UTC  
> Closed at: 2019-12-14 15:45:31 UTC  
> Url: https://github.com/boostorg/nowide/pull/46  

Remove debug code, wrong comment parts, trailing whitespace and superfluous define checks  
  
Also add .gitignore

---

## Comment 1

> Username: Flamefire  
> Created_at: 2019-12-14 14:52:04 UTC  
> Url: https://github.com/boostorg/nowide/pull/46#issuecomment-565724482  

@pdimov Do you know if a conditionally header-only library is possible in BJam? I'd rather not compile iostreams.cpp on non-Windows but I'm not sure if the jam files and CMake install can handle this. For this I used the approach taken by BoostSystem to define a dummy symbol

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-12-14 15:36:03 UTC  
> Url: https://github.com/boostorg/nowide/pull/46#issuecomment-565727878  

It's probably possible, but leaving it this way is arguably better. It allows moving things into the compiled library in the future even on non-Windows without breaking client code.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2019-12-14 15:47:53 UTC  
> Url: https://github.com/boostorg/nowide/pull/46#issuecomment-565728754  

Good point. I was even thinking about putting most of the code into cpp files. This avoids the client dependencies on Boost.Locale and the conversion stuff unless the user wants to use them themselves.

---
