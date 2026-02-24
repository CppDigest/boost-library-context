# #58 Feature/x3 no case [Merged]

> Username: teajay-fr  
> Created at: 2014-10-06 21:16:33 UTC  
> Updated at: 2014-10-18 08:19:23 UTC  
> Merged at: 2014-10-18 07:01:57 UTC  
> Closed at: 2014-10-18 07:01:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/58  

Here's my version of the no_case directive implementation. I haven't modified the symbols parser yet and some test cases still don't pass, they are commented out for.   
I would appreciate to have your comments on how good or bad this fits in the x3 design.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-10-06 22:13:26 UTC  
> Updated_at: 2014-10-06 22:18:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/58#issuecomment-58108587  

I haven't fully studied your code yet, but unless absolutely necessary, we really shouldn't allow any parser invade the "core" layer (e.g. x3/core/parse.hpp and core/skip_over.hpp)  
- Why is it needed to change the context for the parse functions?  
- I can see why you want it for skip_over, but you can do the same by just passing in the current context to skip_over, no? Then, the skipper should do the right thing.

---

## Comment 2

> Username: teajay-fr  
> Created_at: 2014-10-07 08:03:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/58#issuecomment-58149996  

I changed the core components to provide a default behavior for the case sensitive parsing. It might  be possible to provide a the default behavior without touching the core. I'll have a look at that.

---

## Comment 3

> Username: djowel  
> Created_at: 2014-10-07 13:00:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/58#issuecomment-58179999  

You don't need a default AFAICT. You can just search the context for a no-case key, if it is not there, then you do case-sensitive parsing, if it's there, you do case-insensitive parsing.

---

## Comment 4

> Username: teajay-fr  
> Created_at: 2014-10-18 06:29:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/58#issuecomment-59600938  

no_case is ready. Unless you spot things that should be improved, this can be merge.

---

## Comment 5

> Username: djowel  
> Created_at: 2014-10-18 07:01:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/58#issuecomment-59601470  

Nice! I love how you handled symbols and the encoding thing! Awesome!

---

## Comment 6

> Username: teajay-fr  
> Created_at: 2014-10-18 08:19:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/58#issuecomment-59603257  

Thanks for the compliments.

---
