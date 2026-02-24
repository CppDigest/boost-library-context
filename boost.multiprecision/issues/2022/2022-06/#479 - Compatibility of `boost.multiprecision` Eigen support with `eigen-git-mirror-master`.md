# #479 - Compatibility of `boost.multiprecision` Eigen support with `eigen-git-mirror/master` [Closed]

> Username: miquelramirez  
> Created at: 2022-06-30 00:38:50 UTC  
> Updated at: 2023-02-20 18:03:35 UTC  
> Closed at: 2023-02-20 18:03:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/479  

Hello @jzmaddock,  
  
This is not so much a bug report as a heads up on what potentially could be a future incompatibility with newer versions of Eigen.  
  
I have run into the following issue when using the **latest** revision of `boost/multiprecision/eigen.hpp` and **Eigen master branch**, under g++11 (with the C++17 mode enabled):  
  
```  
boost/multiprecision/eigen.hpp:247:12: error: redeclared with 2 template parameters  
  247 |     struct conj_impl;  
      |            ^~~~~~~~~  
....  
/usr/local/include/eigen3/Eigen/src/Core/MathFunctions.h:261:34: note: previous declaration ‘template<class Scalar> struct Eigen::internal::conj_impl’ used 1 template parameter  
  261 | template<typename Scalar> struct conj_impl : conj_default_impl<Scalar> {};  
      |                                  ^~~~~~~~~  
...  
boost/multiprecision/eigen.hpp:256:94: error: wrong number of template arguments (2, should be 1)  
  256 | struct conj_impl<boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>, true>  
      |                                                                                              ^  
In file included from /usr/local/include/eigen3/Eigen/Core:159,  
                 from /usr/local/include/eigen3/Eigen/Dense:1,  
....  
/usr/local/include/eigen3/Eigen/src/Core/MathFunctions.h:261:34: note: provided for ‘template<class Scalar> struct Eigen::internal::conj_impl’  
  261 | template<typename Scalar> struct conj_impl : conj_default_impl<Scalar> {};  
      |                                  ^~~~~~~~~  
/  
```  
  
`Eigen::internal::conj_impl` is currently defined by Eigen as a single argument template. Removing the extra template argument [here](https://github.com/boostorg/multiprecision/blob/20db7371a3dae38721756417d365a29a1cc0321a/include/boost/multiprecision/eigen.hpp#L246) and [here](https://github.com/boostorg/multiprecision/blob/20db7371a3dae38721756417d365a29a1cc0321a/include/boost/multiprecision/eigen.hpp#L256) produces a correct build, but I haven't run the `boost` tests.  
  
Thanks very much for maintaining this very useful component of `boost`!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-07-02 11:01:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/479#issuecomment-1172879515  

That's strange, I don't see any changes here: https://gitlab.com/libeigen/eigen/-/blob/master/Eigen/src/Core/MathFunctions.h#L257  ?

---

## Comment 2

> Username: mglisse  
> Created at: 2022-10-17 16:34:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/479#issuecomment-1281146715  

Link to the Eigen side of things: https://gitlab.com/libeigen/eigen/-/issues/2112 (and https://gitlab.com/libeigen/eigen/-/commit/c0eb5f89a406243f71eae0b705eba4437d9f8565).

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-10-17 16:44:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/479#issuecomment-1281157643  

OK, good.  Is there something that isn't an internal detail we should have specialized instead?
