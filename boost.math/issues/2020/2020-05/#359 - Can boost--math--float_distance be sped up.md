# #359 - Can boost::math::float_distance be sped up? [Open]

> Username: NAThompson  
> Created at: 2020-05-28 14:31:45 UTC  
> Updated at: 2020-06-05 15:32:05 UTC  
> Url: https://github.com/boostorg/math/issues/359  

In the AGM PR, I have found that ~90% of the runtime is spent computing float distances. However, at least for `float` and `double`, the following trivial modification drops the runtime to a negligible fraction of the total runtime:  
  
```  
    int32_t fast_float_distance(float x, float y) {  
        static_assert(sizeof(float) == sizeof(int32_t), "float is incorrect size.");  
        int32_t xi = *reinterpret_cast<int32_t*>(&x);  
        int32_t yi = *reinterpret_cast<int32_t*>(&y);  
        return yi - xi;  
    }  
  
    int64_t fast_float_distance(double x, double y) {  
        static_assert(sizeof(double) == sizeof(int64_t), "double is incorrect size.");  
        int64_t xi = *reinterpret_cast<int64_t*>(&x);  
        int64_t yi = *reinterpret_cast<int64_t*>(&y);  
        return yi - xi;  
    }  
```  
  
It seems like `boost::math::float_distance` is considerably more general than this, but can we dive through a happy path to extract performance in the trivial cases?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-05-28 16:45:22 UTC  
> Url: https://github.com/boostorg/math/issues/359#issuecomment-635463394  

Thats interesting!  Does it pass the tests?

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-05-28 17:15:53 UTC  
> Updated at: 2020-05-28 17:18:12 UTC  
> Url: https://github.com/boostorg/math/issues/359#issuecomment-635482495  

@jzmaddock : Yes; [here's](https://randomascii.wordpress.com/2012/01/23/stupid-float-tricks-2/) some background on the trick.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-05-28 17:53:01 UTC  
> Url: https://github.com/boostorg/math/issues/359#issuecomment-635501473  

OK, but as pointed out in the article, your trick fails when the two inputs differ in sign (this includes when one input is zero).  I also get a negative rather than positive result for say `fast_float_distance(-1, -0.5)`.  All of which can be fixed with some special case handling of course...

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-05-28 18:39:20 UTC  
> Updated at: 2020-05-28 19:02:27 UTC  
> Url: https://github.com/boostorg/math/issues/359#issuecomment-635525364  

@jzmaddock : Yeah, the fact that `agm` requires positive numbers (over the reals) simplifies the logic considerably.  
  
The wins for float128 are pretty huge:  
  
Without the fast float distance:  
  
```  
AGM<boost::multiprecision::float128>       8411 ns         8377 ns        82937  
```  
  
with it:  
  
```  
AGM<boost::multiprecision::float128>       2241 ns         2230 ns       313072  
```  
  
Implementation:  
  
```  
#ifdef BOOST_HAS_FLOAT128  
    __int128_t fast_float_distance(boost::multiprecision::float128 x, boost::multiprecision::float128 y) {  
        static_assert(sizeof(boost::multiprecision::float128) == sizeof(__int128_t), "double is incorrect size.");  
        __int128_t xi = *reinterpret_cast<__int128_t*>(&x);  
        __int128_t yi = *reinterpret_cast<__int128_t*>(&y);  
        return yi - xi;  
    }  
#endif  
```  
  
I couldn't get it to work with `long double`, sadly.

---

## Comment 5

> Username: cosurgi  
> Created at: 2020-06-05 15:32:05 UTC  
> Url: https://github.com/boostorg/math/issues/359#issuecomment-639567383  

`long double` is quite irritating sometimes. However it's also useful ;)
