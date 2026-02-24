# #148 - icu_regex_traits::translate_nocase doesn't use case-folding [Closed]

> Username: Dani-Hub  
> Created at: 2021-08-06 17:30:52 UTC  
> Updated at: 2021-10-06 12:45:54 UTC  
> Closed at: 2021-10-06 12:45:53 UTC  
> Url: https://github.com/boostorg/regex/issues/148  

Inspecting the Boost Regex 1.77.0 code of `icu_regex_traits::translate_nocase` I was surprised to notice that this function is implemented in terms of `u_tolower` instead of `u_foldCase`, which performs proper case-folding (on a single codepoint level). I'm wondering whether this is an intentional design (To be comparable with the non-ICU traits) or not?   
  
Consider this issue as an initial start of this topic. I haven't yet implemented such a change but would be interested in doing so once I have understood the rationale for the current state of affairs.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-08-08 10:42:10 UTC  
> Url: https://github.com/boostorg/regex/issues/148#issuecomment-894777652  

To be honest I think it's an oversight on my part.

---

## Comment 2

> Username: Dani-Hub  
> Created at: 2021-08-08 11:51:46 UTC  
> Url: https://github.com/boostorg/regex/issues/148#issuecomment-894785952  

Thanks for your feedback, @jzmaddock! Unless you are already working on that, I'm willing to provide a corresponding PULL request.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-08-08 12:44:47 UTC  
> Url: https://github.com/boostorg/regex/issues/148#issuecomment-894792302  

That would be great thanks!

---

## Comment 4

> Username: Dani-Hub  
> Created at: 2021-08-08 16:34:15 UTC  
> Url: https://github.com/boostorg/regex/issues/148#issuecomment-894822538  

@jzmaddock : A pull request has been prepared, but it seems that I need a maintainer first to approve running workflows.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-10-06 12:45:53 UTC  
> Url: https://github.com/boostorg/regex/issues/148#issuecomment-936168664  

Fixed in develop.
