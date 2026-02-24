# #448 - Please document hana::transform side effect ordering [Open]

> Username: dcolascione  
> Created at: 2019-05-22 18:32:47 UTC  
> Updated at: 2019-05-22 19:54:28 UTC  
> Url: https://github.com/boostorg/hana/issues/448  

GCC appears to evaluate transform function calls out of order, which I think it's allowed to do because function argument evaluation order is undefined. Hana should explicitly document the lack of ordering guarantees in transform.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-05-22 19:53:33 UTC  
> Updated at: 2019-05-22 19:54:28 UTC  
> Url: https://github.com/boostorg/hana/issues/448#issuecomment-494945800  

It is documented in the manual here: http://boostorg.github.io/hana/index.html#tutorial-algorithms-effects  
  
The issue with documenting it for `hana::transform` itself is that each _data-type_ can have it's own implementation that behaves differently. I'm guessing the one you are considering is for any `hana::Sequence`.  
  
Take a look at the algorithms for `hana::Monad`.
