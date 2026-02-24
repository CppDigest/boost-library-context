# #317 - Dirichlet Distribution [Closed]

> Username: mrityunjay-tripathi  
> Created at: 2020-02-24 14:54:38 UTC  
> Updated at: 2020-02-24 23:18:15 UTC  
> Closed at: 2020-02-24 23:18:15 UTC  
> Url: https://github.com/boostorg/math/issues/317  

There is no [Dirichlet distribution](https://en.wikipedia.org/wiki/Dirichlet_distribution) in either armadillo or boost. Can we implement it in boost?  
Reference: https://mast.queensu.ca/~communications/Papers/msc-jiayu-lin.pdf

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-02-24 15:04:02 UTC  
> Updated at: 2020-02-24 15:04:30 UTC  
> Url: https://github.com/boostorg/math/issues/317#issuecomment-590365865  

I just looked at your links; seems useful and straightforward to implement numerically. Unfortunately, I personally am backlogged with wavelets and multivariate interpolation, so you'll either have to submit a PR or convince @pabristow or @jzmaddock that it's cool enough for them to do it.  
  
As to consolidation, could you add it to the feature wishlist?

---

## Comment 2

> Username: mrityunjay-tripathi  
> Created at: 2020-02-24 15:33:46 UTC  
> Url: https://github.com/boostorg/math/issues/317#issuecomment-590386673  

> so you'll either have to submit a PR or convince @pabristow or @jzmaddock that it's cool enough for them to do it.  
  
No worries. I can work on it.  
> As to consolidation, could you add it to the feature wishlist?  
  
Sure. :)

---

## Comment 3

> Username: pabristow  
> Created at: 2020-02-24 17:15:22 UTC  
> Url: https://github.com/boostorg/math/issues/317#issuecomment-590444891  

Much of the work in adding a distribution is in devising a suite of tests to confirm correctness and precision/accuracy, and writing documentation, and an example of use.  A working implementation gives a head start.  If you can help with these, then I might be persuaded to tackle it.

---

## Comment 4

> Username: mrityunjay-tripathi  
> Created at: 2020-02-24 17:42:17 UTC  
> Url: https://github.com/boostorg/math/issues/317#issuecomment-590459032  

@pabristow Sure. I am currently getting familiar with the codebase.

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-02-24 23:18:12 UTC  
> Url: https://github.com/boostorg/math/issues/317#issuecomment-590598007  

Just to keep things tidy, I'm going to close this and let the feature wishlist be the point of authority for this issue.
