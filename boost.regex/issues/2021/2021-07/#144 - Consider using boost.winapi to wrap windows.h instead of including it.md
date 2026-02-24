# #144 - Consider using boost.winapi to wrap windows.h instead of including it [Closed]

> Username: Ryan-rsm-McKenzie  
> Created at: 2021-07-11 06:57:26 UTC  
> Updated at: 2021-10-06 10:11:10 UTC  
> Closed at: 2021-10-06 10:11:10 UTC  
> Url: https://github.com/boostorg/regex/issues/144  

I'm not sure if you're aware, but [Boost.WinAPI](https://github.com/boostorg/winapi) exists as a means for boost libs to invoke winapi methods without actually including the header. The change to a header only lib in 1.76 has managed to break every single project I use Boost.Regex in, as I was under the assumption it was a "safe" (in terms of not polluting the global namespace) header to include.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-07-11 08:14:49 UTC  
> Url: https://github.com/boostorg/regex/issues/144#issuecomment-877760173  

WinAPI doesn't have what I need - that was the first place I looked!

---

## Comment 2

> Username: Ryan-rsm-McKenzie  
> Created at: 2021-07-11 09:50:12 UTC  
> Url: https://github.com/boostorg/regex/issues/144#issuecomment-877771540  

Have you considered opening an issue to have them added?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-10-06 10:11:10 UTC  
> Url: https://github.com/boostorg/regex/issues/144#issuecomment-935901825  

Fixed in develop.
