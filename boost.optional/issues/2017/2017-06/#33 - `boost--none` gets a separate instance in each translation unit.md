# #33 - `boost::none` gets a separate instance in each translation unit [Closed]

> Username: akrzemi1  
> Created at: 2017-06-05 10:46:42 UTC  
> Updated at: 2019-12-19 19:52:15 UTC  
> Closed at: 2019-12-19 19:52:15 UTC  
> Url: https://github.com/boostorg/optional/issues/33  



---

## Comment 1

> Username: akrzemi1  
> Created at: 2017-06-05 10:49:54 UTC  
> Url: https://github.com/boostorg/optional/issues/33#issuecomment-306159747  

Provide a (still header-only) implementation that only renders a single instance of `boost::none`, e.g. using an enum.

---

## Comment 2

> Username: Lastique  
> Created at: 2019-10-02 11:30:18 UTC  
> Updated at: 2019-10-02 11:33:33 UTC  
> Url: https://github.com/boostorg/optional/issues/33#issuecomment-537450746  

As commented in https://github.com/boostorg/core/issues/63#issuecomment-537450203, unscoped enums can implicitly convert to `int`, which can cause trouble with function overloads.  
  
You may want to consider marking `none` with `inline` on C++17 compilers (conditioned on `BOOST_NO_CXX17_INLINE_VARIABLES`).
