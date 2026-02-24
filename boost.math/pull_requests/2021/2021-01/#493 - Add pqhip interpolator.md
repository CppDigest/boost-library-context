# #493 Add pqhip interpolator [Closed]

> Username: NAThompson  
> Created at: 2021-01-14 21:18:51 UTC  
> Updated at: 2021-01-20 21:33:57 UTC  
> Closed at: 2021-01-20 21:33:52 UTC  
> Url: https://github.com/boostorg/math/pull/493  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-01-15 19:03:45 UTC  
> Url: https://github.com/boostorg/math/pull/493#issuecomment-761127994  

@mborland : Looks like the `[CI SKIP]` tag on the commit messages no longer stops a build from being kicked off. Do you know how to stop it?

---

## Comment 2

> Username: mborland  
> Created_at: 2021-01-15 19:17:45 UTC  
> Url: https://github.com/boostorg/math/pull/493#issuecomment-761135657  

@NAThompson Github actions does not support skipping completely like Travis, Appveyor, or CircleCI. The CI yaml file instead kicks off a build and then fails quickly. [Here](https://github.com/mstachniuk/ci-skip) is the plugin we use to make that happen. I will say it is one of the cons of actions.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-01-20 21:33:52 UTC  
> Url: https://github.com/boostorg/math/pull/493#issuecomment-763963103  

This is unfortunately, a terrible interpolator.

---
