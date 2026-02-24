# #465 - CMakeLists.txt uses BOOST_COMPUTE_ in options [Closed]

> Username: marbre  
> Created at: 2015-06-06 08:30:45 UTC  
> Updated at: 2015-06-09 06:46:15 UTC  
> Closed at: 2015-06-09 06:46:05 UTC  
> Url: https://github.com/boostorg/compute/issues/465  

I have created an ebuild of BoostCompute for the [Gentoo Science Overlay](https://github.com/gentoo-science/sci/tree/master/dev-libs/boost-compute). We use cmake for installation and I would like to enable some of the options provided in the `CMakeLists.txt` via Gentoo specific use flags. BoostCompute defines most options with `option(BOOST_COMPUTE_HAVE_`. Is the `BOOST_COMPUTE_` prefix really necessary? For us it would be easier if the prefix is dropped, since with than could just functions like `$(cmake-utils_use_has cuda CUDA)` (see [devmanual](https://devmanual.gentoo.org/eclass-reference/cmake-utils.eclass/)), which adds `-DHAVE_CUDA=ON` if the cuda use flag is added and `-DHAVE_CUDA=OFF` otherwise.  
  
I would like to hear what you think about this. If it is acceptable to drop the prefix, I can also send a pull request.

---

## Comment 1

> Username: ddemidov  
> Created at: 2015-06-06 08:54:17 UTC  
> Updated at: 2015-06-06 09:10:23 UTC  
> Url: https://github.com/boostorg/compute/issues/465#issuecomment-109553855  

Boost.Compute is header only, so why would one need the use flags? The options control what examples/perf tests are built, do those need to be installed system-wide?

---

## Comment 2

> Username: marbre  
> Created at: 2015-06-06 09:02:06 UTC  
> Url: https://github.com/boostorg/compute/issues/465#issuecomment-109554374  

Well, we also got an `examples` use flag which allows the user to install them. But no, it it only affects the examples there is no real need. As it seems that also the tests are unaffected (just took a quick look), I think it's not worth the effort of dropping the prefix.

---

## Comment 3

> Username: kylelutz  
> Created at: 2015-06-09 03:31:44 UTC  
> Url: https://github.com/boostorg/compute/issues/465#issuecomment-110212564  

Yeah, the flags in the `CMakeLists.txt` mainly affect the examples, benchmarks, and tests that get compiled. The "real" Boost.Compute code is all header-only and isn't dependent on any of those flags.

---

## Comment 4

> Username: marbre  
> Created at: 2015-06-09 06:46:05 UTC  
> Url: https://github.com/boostorg/compute/issues/465#issuecomment-110247519  

Thanks for clarification.
