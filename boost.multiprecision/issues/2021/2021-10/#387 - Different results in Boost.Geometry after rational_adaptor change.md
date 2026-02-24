# #387 - Different results in Boost.Geometry after rational_adaptor change. [Closed]

> Username: awulkiew  
> Created at: 2021-10-26 19:12:31 UTC  
> Updated at: 2021-10-27 21:46:54 UTC  
> Closed at: 2021-10-27 18:31:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/387  

At Boost.Geometry we noticed that one test case started to fail after this change: https://github.com/boostorg/multiprecision/commit/4815cb08888cfbb81037bc8bd1fbbe8fe5ad3a54 / PR https://github.com/boostorg/multiprecision/pull/366  
in particular this one:  
https://github.com/boostorg/geometry/blob/d0838774aae3c73f208f89623e0a1423973381b7/test/algorithms/set_operations/union/union_other_types.cpp#L145  
where `cpp_rational` aka `number<backends::rational_adaptor<backends::cpp_int_backend<...>>...>` is used as coordinate type of tested geometries.  
  
The results are different than they were in the past and different than we get with `boost::rational` and other coordinate types.  
  
To reproduce:  
```  
cd libs/geometry  
git fetch origin  
git checkout d0838774a  
../../b2 test/algorithms/set_operations/union//algorithms_union_other_types  
```  
  
If you have any suggestions what I could do to help you find the issue feel free to ask.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-10-27 18:35:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/387#issuecomment-953205245  

This took a bit of tracking down, somewhere in your code there is a default constructed value which is later used in a comparison.  The regression in Multiprecision was that default constructed values changed from 0 to 1 (now fixed in commit referenced above).  You may wish to track down that stray default-constructed (uninitialised?) value though.

---

## Comment 2

> Username: awulkiew  
> Created at: 2021-10-27 21:46:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/387#issuecomment-953335701  

Thanks! Yes, there are many places like this in Geometry where we assume that default constructed values are equal to 0 which corresponds to value initialization of fundamental types. But I agree that we should rather assign 0 explicitly.
