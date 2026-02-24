# #441 Persistent cache [Open]

> Username: salmanulhaq  
> Created at: 2015-03-22 06:49:02 UTC  
> Updated at: 2016-10-19 04:04:48 UTC  
> Url: https://github.com/boostorg/compute/pull/441  

-    Made `clear_persistent_cache` static  
-    Catching exception if there's no `.boost_compute` folder (or in use)  
-    Followed @ddemidov suggestion on computing hash for persistent cache lookup  
  
@kylelutz have to re-use `appdata` path snippet since `appdata_path()` is private within `program` but `clear_persistent_cache` suits better in `program_cache`.

---

## Comment 1

> Username: ddemidov  
> Created_at: 2015-03-22 16:35:29 UTC  
> Url: https://github.com/boostorg/compute/pull/441#issuecomment-84649994  

You forgot to actually change the implementation of `boost::compute::detail::sha1`. I went and made the same improvement for my own library [here](https://github.com/ddemidov/vexcl/commit/ab9f0f5a8ed1c294f42b71c64297b66e2c134500); feel free to reuse it.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-03-22 16:46:25 UTC  
> Url: https://github.com/boostorg/compute/pull/441#discussion_r26904749  

So instead of doing all this here, you should call the [`detail::appdath_path()`](https://github.com/kylelutz/compute/blob/develop/include/boost/compute/detail/path.hpp#L30) function defined in `<boost/compute/detail/path.hpp>`. I recently extracted this code from the `program` class so you may have to rebase on top of the `develop` branch to get it.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-03-22 16:48:15 UTC  
> Updated_at: 2015-03-22 16:48:27 UTC  
> Url: https://github.com/boostorg/compute/pull/441#discussion_r26904770  

I don't think you need both an existence check and a try/catch block. I'd lean towards just using the `if(exists(appdata))` condition and then removing the try/catch.

---
