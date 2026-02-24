# #519 - CI Failure with Clang on MP tests [Closed]

> Username: mborland  
> Created at: 2021-02-04 16:42:01 UTC  
> Updated at: 2021-02-04 22:01:27 UTC  
> Closed at: 2021-02-04 21:53:10 UTC  
> Url: https://github.com/boostorg/math/issues/519  

Several new CI failures on Github Actions from lambert_w in the MP tests using Clang. Details [here](https://github.com/boostorg/math/runs/1831098749?check_suite_focus=true#step:16:654) and below:  
  
```  
../../../boost/math/special_functions/lambert_w.hpp:2015:19: error: no matching conversion for static_cast from 'typename std::enable_if<is_compatible_arithmetic_type<double, number<cpp_dec_float<50, int, void>, et_on> >::value && !is_equivalent_number_type<double, number<cpp_dec_float<50, int, void>, et_on> >::value, detail::expression<detail::multiply_immediates, number<cpp_dec_float<50, int, void>, et_on>, double> >::type' (aka 'boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50, int, void>, boost::multiprecision::et_on>, double, void, void>') to 'calc_type' (aka 'double')  
    calc_type y = static_cast<calc_type>(z * wm1es[n - 1]); // Equation 26,  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-02-04 17:16:10 UTC  
> Url: https://github.com/boostorg/math/issues/519#issuecomment-773470074  

Yes I know :(  
  
Both Multiprecision and Math were passing individually but post-merging the integration tests failed, and only for clang for some strange reason.... investigating now.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-04 22:01:27 UTC  
> Url: https://github.com/boostorg/math/issues/519#issuecomment-773631979  

This should be fixed in multiprecision develop, but it will be a few hours before the super-project syncs up so I'll kick off a new build in the morning.
