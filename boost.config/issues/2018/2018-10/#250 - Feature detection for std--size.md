# #250 - Feature detection for std::size [Closed]

> Username: NAThompson  
> Created at: 2018-10-29 05:17:00 UTC  
> Updated at: 2018-11-08 16:25:24 UTC  
> Closed at: 2018-11-08 08:58:52 UTC  
> Url: https://github.com/boostorg/config/issues/250  

For the [Catmull-Rom PR](https://github.com/boostorg/math/pull/95), we require C++17 `std::size`. I'm currently simulating C++17 feature support with `requires cxx17_if_constexpr`, which seems to work well enough (though [this failure](https://travis-ci.org/boostorg/math/jobs/447629613) makes the mind boggle). However, it might be nicer to have the exact feature.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-11-08 08:58:52 UTC  
> Url: https://github.com/boostorg/config/issues/250#issuecomment-436920521  

I think all the major compilers are supporting SD-6 feature detection now (https://isocpp.org/std/standing-documents/sd-6-sg10-feature-test-recommendations#recs.cpp17) so there's not much point in duplicating that effort.  Can you use `__cpp_lib_nonmember_container_access` ?  You will need to have included one of the std lib container headers, but I think you include `<vector>` anyway for that one?

---

## Comment 2

> Username: morinmorin  
> Created at: 2018-11-08 13:18:37 UTC  
> Url: https://github.com/boostorg/config/issues/250#issuecomment-436990789  

You can also use `boost::size` from Boost.Range, I think.

---

## Comment 3

> Username: NAThompson  
> Created at: 2018-11-08 16:25:24 UTC  
> Url: https://github.com/boostorg/config/issues/250#issuecomment-437058555  

@jzmaddock: Good to know about these macros, but I was thinking more about the feature detection in Jamfiles. Maybe the Jamfile syntax should mirror the SD-6 feature detection?
