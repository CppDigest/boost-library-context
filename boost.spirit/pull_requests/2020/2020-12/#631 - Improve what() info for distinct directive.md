# #631 Improve what() info for distinct directive [Merged]

> Username: sehe  
> Created at: 2020-12-01 20:56:53 UTC  
> Updated at: 2020-12-14 15:17:48 UTC  
> Merged at: 2020-12-03 01:13:54 UTC  
> Closed at: 2020-12-03 01:13:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/631  

Like on similar directives, propagate the info from the subject parser  
for improved debug/diagnostics.  
  
The idea taken from and demonstrated here: https://stackoverflow.com/questions/65087474/how-to-get-boost-spirit-build-in-error-message-distinct-possible-symbols/65098082#65098082  
  
With this change, user won't need to tediously create a custom `my_distinct` directive just to override the `what()` behaviour.

---

## Comment 1

> Username: djowel  
> Created_at: 2020-12-02 07:24:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/631#issuecomment-737046907  

"The Travis CI build is in progress"... will this ever finish?... :-/

---

## Comment 2

> Username: djowel  
> Created_at: 2020-12-03 01:13:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/631#issuecomment-737594490  

"The Travis CI build is in progress"... Sigh... I'll take the chance and merge this now.

---
