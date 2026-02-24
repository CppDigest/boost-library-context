# #25 - What "does not try to implement a functional language in C++" exatly mean? [Closed]

> Username: viboes  
> Created at: 2015-10-04 00:40:04 UTC  
> Updated at: 2015-10-30 22:50:07 UTC  
> Closed at: 2015-10-30 22:50:07 UTC  
> Url: https://github.com/boostorg/hof/issues/25  



---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:14:40 UTC  
> Url: https://github.com/boostorg/hof/issues/25#issuecomment-145426499  

This is too contrast it too say FC++ or Boost.Phoenix, which reimplements C++ in a functional way. As such, the functions in Fit do not curry by default, nor is there special functions for control flow(like [here](http://www.boost.org/doc/libs/1_59_0/libs/phoenix/doc/html/phoenix/modules/statement.html)). Does that make sense?

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:50:57 UTC  
> Url: https://github.com/boostorg/hof/issues/25#issuecomment-145691170  

I believe them did that because we didn't have lambdas.  
BTW some works respect to FC++ and Boost.Phoenix are missing in the documentation. During the review you will need to show a comparison with these libraries.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-30 22:50:07 UTC  
> Url: https://github.com/boostorg/hof/issues/25#issuecomment-152668712  

This is updated in the latest updates to the docs.
