# #1059 Remove last usage of boost::array [Merged]

> Username: NAThompson  
> Created at: 2023-12-20 01:16:58 UTC  
> Updated at: 2023-12-20 09:08:47 UTC  
> Merged at: 2023-12-20 04:48:42 UTC  
> Closed at: 2023-12-20 04:48:42 UTC  
> Url: https://github.com/boostorg/math/pull/1059  

Now that boost.math is C++14, remove boost::array in order to improve orthogonality of the library.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2023-12-20 01:17:23 UTC  
> Updated_at: 2023-12-20 01:18:54 UTC  
> Url: https://github.com/boostorg/math/pull/1059#issuecomment-1863693005  

@mborland , @jzmaddock : Pretty trivial but hopefully helpful.  
  
Should we do the same in `odeint`?

---

## Comment 2

> Username: mborland  
> Created_at: 2023-12-20 04:06:33 UTC  
> Url: https://github.com/boostorg/math/pull/1059#issuecomment-1863814636  

> @mborland , @jzmaddock : Pretty trivial but hopefully helpful.  
>   
>   
>   
> Should we do the same in `odeint`?  
  
Everything C++03 should be ripped out of odeint. I think the biggest offender to compile time in there is MPL.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-12-20 09:08:46 UTC  
> Url: https://github.com/boostorg/math/pull/1059#issuecomment-1864109150  

Thanks Matt!

---
