# #352 - No isnan for real_concept [Closed]

> Username: NAThompson  
> Created at: 2020-05-06 11:42:50 UTC  
> Updated at: 2020-05-07 14:31:13 UTC  
> Closed at: 2020-05-07 14:31:12 UTC  
> Url: https://github.com/boostorg/math/issues/352  

It looks like `isnan` slipped through the cracks on `real_concept`:  
  
```  
test/math_unit_test.hpp:79:14: error: no matching function for call to 'isnan(boost::math::concepts::real_concept&)'  
   79 |     if (isnan(computed))  
      |         ~~~~~^~~~~~~~~~  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-05-06 12:08:55 UTC  
> Url: https://github.com/boostorg/math/issues/352#issuecomment-624611239  

Fix coming for `isnan`, but also we have this problem, which seems harder:  
  
```  
test/math_unit_test.hpp:94:39: error: invalid static_cast from type 'boost::math::concepts::real_concept' to type 'int64_t' {aka 'long long int'}  
   94 |         detail::total_ulp_distance += static_cast<int64_t>(dist);  
      |                                       ^~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-05-06 16:30:14 UTC  
> Url: https://github.com/boostorg/math/issues/352#issuecomment-624753270  

It was deliberate - we assumed C++03 support only.  The call to isnan should be protected against macro expansion as well - there are sadly still systems out there that #define that.  
  
Does:  
  
```  
using boost::math::isnan;  
using std::isnan;  
```  
  
Fix things?  
  
Likewise the lack of a (lossy) cast to integer is deliberate.  
  
Use either boost::math::itrunc, ltrunc or lltrunc.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-05-07 14:00:32 UTC  
> Url: https://github.com/boostorg/math/issues/352#issuecomment-625273526  

Ok, so adding `max` and `isnan` to `real_concept` is not desired? Or is it reasonable given that `real_concept` is only for internal use?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-05-07 14:28:09 UTC  
> Url: https://github.com/boostorg/math/issues/352#issuecomment-625289343  

>Or is it reasonable given that real_concept is only for internal use?  
  
It's purpose is to model the "minimum conceptual requirement" for a number type, adding stuff changes what that requirement is.  So no, not such a good idea I'm afraid!

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-05-07 14:31:12 UTC  
> Url: https://github.com/boostorg/math/issues/352#issuecomment-625291109  

@jzmaddock : Ok, cool; I'll get rid of those diffs in the feigenbaum PR.
