# #440 added clear_persistent_cache and minor update to persistent caching [Open]

> Username: salmanulhaq  
> Created at: 2015-03-21 20:44:21 UTC  
> Updated at: 2015-03-22 18:45:38 UTC  
> Url: https://github.com/boostorg/compute/pull/440  

Added `clear_persistent_cache()` in `program_cache.hpp` which removes `.boost_compute` directory.  
  
Minor update to persistent caching: instead of computing sha1 hash of just program source, I've also added compiler options and selected device's name so if anything changes, program would be built again.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-03-21 22:52:57 UTC  
> Updated_at: 2015-03-22 06:44:01 UTC  
> Url: https://github.com/boostorg/compute/pull/440#discussion_r26897651  

Instead of copying these lines, could you just call `detail::appdata_path()` directly?

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-03-21 23:13:26 UTC  
> Url: https://github.com/boostorg/compute/pull/440#issuecomment-84469977  

Also, could you open a pull request against the `develop` branch (that's where we do testing/integration before updating the more stable `master` branch). Thanks!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-03-21 23:14:30 UTC  
> Updated_at: 2015-03-22 06:44:01 UTC  
> Url: https://github.com/boostorg/compute/pull/440#discussion_r26897921  

I think this could be a static method (so users can just call `program_cache::clear_persistent_cache()` directly without having to get/create a `program_cache` object).

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-03-21 23:15:10 UTC  
> Updated_at: 2015-03-22 06:44:01 UTC  
> Url: https://github.com/boostorg/compute/pull/440#discussion_r26897959  

I can't remember, but will this through an exception if the offline cache directory hasn't been created yet? If so we should catch/ignore that.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2015-03-21 23:15:57 UTC  
> Updated_at: 2015-03-22 06:44:01 UTC  
> Url: https://github.com/boostorg/compute/pull/440#discussion_r26897963  

As Denis commented, this additional information should already be stored inside the program source and therefore I don't think we need to store it here.

---

## Comment 6

> Username: ddemidov  
> Created_at: 2015-03-22 05:38:28 UTC  
> Url: https://github.com/boostorg/compute/pull/440#issuecomment-84525139  

Regarding @keryell's note: one possible solution is to make `compute::detail::sha1` a functor, where `operator()(const std::string&)` calls `process_bytes()` repeatedly (see the current implementation of `details::sha1` [here](https://github.com/kylelutz/compute/blob/aa15cd6ac4cd76bf7129a0c653dde2e70e8c089c/include/boost/compute/detail/sha1.hpp#L25)). It could also provide a conversion to string operator, so that computing  the hash could be:  
  
``` cpp  
detail::sha1 hash;  
hash(source);  
hash(device_name);  
hash(compiler_version);  
  
load_program_binary(hash, context);  
```

---

## Comment 7

> Username: keryell  
> Created_at: 2015-03-22 18:45:38 UTC  
> Url: https://github.com/boostorg/compute/pull/440#issuecomment-84672596  

Yes you are right, since boost/uuid/sha1.hpp has already the right interface. So there is no need to have a Merkle's tree approach that would create several sha1 intermediate objects.  
Great!

---
