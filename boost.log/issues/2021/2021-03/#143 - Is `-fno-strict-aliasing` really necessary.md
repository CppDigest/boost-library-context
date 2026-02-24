# #143 - Is `-fno-strict-aliasing` really necessary? [Open]

> Username: Kojoley  
> Created at: 2021-03-07 16:03:12 UTC  
> Updated at: 2022-06-05 15:30:19 UTC  
> Url: https://github.com/boostorg/log/issues/143  

The library builds with `-fno-strict-aliasing` on GCC:  
https://github.com/boostorg/log/blob/8a880720be5a990fdd0f864244bfff1a39a69d83/build/Jamfile.v2#L202  
The comment says that it is because of other Boost libraries but does not name them or link bug reports, making it hard to justify the flag. It should be a good thing to re-raise the issue on the libraries that still had not fixed it, as strict aliasing is a severe violation.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-03-07 19:06:43 UTC  
> Url: https://github.com/boostorg/log/issues/143#issuecomment-792334661  

At this point I can't remember where these violations were, I just remember compilers throwing warnings about strict aliasing violations in code other than Boost.Log. Maybe these were fixed by now.

---

## Comment 2

> Username: ClaymorePT  
> Created at: 2022-06-05 13:50:48 UTC  
> Url: https://github.com/boostorg/log/issues/143#issuecomment-1146808612  

I'm also seeing this  
  
```  
[build] /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/mpl/aux_/integral_wrapper.hpp:73:88: error: the result of the conversion is unspecified because ‘(0 - 1)’ is outside the range of type ‘boost::log::v2s_mt_posix::lock_access_mode’ [-Werror=conversion]  
[build]    73 |     typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
[build]       |                                                                                 ~~~~~~~^~~~  
[build] /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/mpl/integral_c.hpp:31:54: note: in definition of macro ‘AUX_WRAPPER_INST’  
[build]    31 | #define AUX_WRAPPER_INST(value) AUX_WRAPPER_NAME< T, value >  
[build]       |                                                      ^~~~~  
[build] /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/mpl/aux_/integral_wrapper.hpp:73:31: note: in expansion of macro ‘BOOST_MPL_AUX_STATIC_CAST’  
[build]    73 |     typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
[build]       |                               ^~~~~~~~~~~~~~~~~~~~~~~~~  
[build] compilation terminated due to -Wfatal-errors.  
```

---

## Comment 3

> Username: Lastique  
> Created at: 2022-06-05 15:30:19 UTC  
> Url: https://github.com/boostorg/log/issues/143#issuecomment-1146834533  

@ClaymorePT This is off-topic here, but should be resolved with 77f1e20bd69c2e7a9e25e6a9818ae6105f7d070c.
