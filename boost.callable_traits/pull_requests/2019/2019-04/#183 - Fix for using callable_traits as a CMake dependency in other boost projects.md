# #183 Fix for using callable_traits as a CMake dependency in other boost projects [Merged]

> Username: HDembinski  
> Created at: 2019-04-14 14:42:57 UTC  
> Updated at: 2019-11-05 09:07:02 UTC  
> Merged at: 2019-11-05 03:17:31 UTC  
> Closed at: 2019-11-05 03:17:31 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/183  

This fixes the problems described in #182.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2019-06-02 19:40:25 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/183#issuecomment-498059434  

Ping, could this PR please be merged? Thank you :)

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-10-19 13:23:59 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/183#issuecomment-544143454  

Hi again, what is the show stopper for this PR?  
  
Sorry, it is a large patch. Most is just renaming things so that it is consistent with boost.core and other boost lbraries. If you don't like the renaming of CMake variables, I can revert that and only make the changes that are visible to other projects.  
  
What really matters are two lines:  
https://github.com/boostorg/callable_traits/pull/183/files#diff-af3b638bc2a3e6c650974192a53c7291R61  
and  
https://github.com/boostorg/callable_traits/pull/183/files#diff-af3b638bc2a3e6c650974192a53c7291R67  
  
The first one puts the common Boost:: prefix in front of the library name. This is making `callable_traits` consistent with the other boost libraries and avoids conflicts with any possible forks of `callable_traits`. The second line is very important. If this is not there, any project which includes `callable_traits` (like Boost.Histogram) fails.

---

## Comment 3

> Username: badair  
> Created_at: 2019-11-05 03:18:13 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/183#issuecomment-549645673  

Merged, sorry for the severe delay.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2019-11-05 09:06:58 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/183#issuecomment-549730209  

Thanks!

---
