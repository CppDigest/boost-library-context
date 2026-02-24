# #86 Updated the check-for-self in x3::variant [Merged]

> Username: JamesWidman  
> Created at: 2015-04-29 19:40:52 UTC  
> Updated at: 2015-04-29 22:57:07 UTC  
> Merged at: 2015-04-29 22:57:07 UTC  
> Closed at: 2015-04-29 22:57:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/86  

I ran into some compile-time errors when trying to copy an object of a type derived from x3::variant.  
  
The root of the issue seems to be the SFINAE check on the members operator=; namely: when variant's move-assignment operator template is considered during overload resolution, it deduces 'A&' for template parameter T, and then reference-collapsing transforms 'variant& &&' into 'variant&', and thus the template ends up being a better choice than the implicitly-declared copy-assignment operator. This leads to a nice tall error message.  
  
It seems like we just need a test for "self" as part of the SFINAE check on that operator template---which we used to have, but it appears to have been commented out at some point (for reasons not immediately clear to me).  
  
In this commit, four of the member templates now use the same compact SFINAE check in the same way (as a default template argument). It seems to do the right thing for me.

---

## Comment 1

> Username: JamesWidman  
> Created_at: 2015-04-29 19:42:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/86#issuecomment-97557252  

Well that looks much more sane than PR #85. (:

---

## Comment 2

> Username: K-ballo  
> Created_at: 2015-04-29 19:43:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/86#issuecomment-97557484  

> Well that looks much more sane than PR #18   
  
Indeed! :)

---

## Comment 3

> Username: djowel  
> Created_at: 2015-04-29 22:56:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/86#issuecomment-97609607  

nice!

---
