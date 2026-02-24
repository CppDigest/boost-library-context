# #48 - -Wzero-as-null-pointer-constant leads to compiler error in g++ [Open]

> Username: joernschellhaas  
> Created at: 2020-01-15 14:29:56 UTC  
> Updated at: 2020-01-28 21:36:02 UTC  
> Url: https://github.com/boostorg/chrono/issues/48  

As of boost 1.72, I'm getting "error: zero as null pointer constant" when using e.g. `boost::chrono::milliseconds`. This can IMO be fixed by using `nullptr` instead.  
  
I've spotted the following places, but there might be more:  
* duration.hpp:458  
* duration.hpp:481  
* duration_put.hpp:216  
* time_point.hpp:185

---

## Comment 1

> Username: viboes  
> Created at: 2020-01-28 21:24:26 UTC  
> Url: https://github.com/boostorg/chrono/issues/48#issuecomment-579463147  

Boost is compatible with C++98.   
  
A PR is more than welcome if you manage with this fact.

---

## Comment 2

> Username: joernschellhaas  
> Created at: 2020-01-28 21:35:27 UTC  
> Updated at: 2020-01-28 21:36:02 UTC  
> Url: https://github.com/boostorg/chrono/issues/48#issuecomment-579467500  

Seems that [NULL](https://en.cppreference.com/w/cpp/types/NULL) can be used to ensure compatibility. Not sure if I'll create a PR.
