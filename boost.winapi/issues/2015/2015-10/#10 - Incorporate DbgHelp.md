# #10 - Incorporate DbgHelp [Closed]

> Username: klemens-morgenstern  
> Created at: 2015-10-18 14:23:59 UTC  
> Updated at: 2015-10-18 19:17:58 UTC  
> Closed at: 2015-10-18 18:16:33 UTC  
> Url: https://github.com/boostorg/winapi/issues/10  

I need a few functions from DbgHelp.h for an extension of boost.DLL, would this fit into this library? It's not the winapi strictly speaking, but it's so heavily dependent on that, that I'd like to add it here as soon as it's tested.

---

## Comment 1

> Username: Lastique  
> Created at: 2015-10-18 18:16:33 UTC  
> Url: https://github.com/boostorg/winapi/issues/10#issuecomment-149036271  

No, I think this submodule is not the right place for it.  
  
Why don't you just #include DbgHelp.h in Boost.DLL?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2015-10-18 19:12:56 UTC  
> Updated at: 2015-10-18 19:17:58 UTC  
> Url: https://github.com/boostorg/winapi/issues/10#issuecomment-149039829  

Because it incorporates all this Winapi stuff, which I want to avoid for a header-only library.  
  
But then I will just keep my header in Boost.DLL, I just thought it might be of interest here.
