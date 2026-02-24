# #1078 Avoid unused variable warning in ibeta_inverse [Merged]

> Username: andrjohns  
> Created at: 2024-02-01 12:55:05 UTC  
> Updated at: 2024-02-04 17:32:30 UTC  
> Merged at: 2024-02-04 17:32:30 UTC  
> Closed at: 2024-02-04 17:32:30 UTC  
> Url: https://github.com/boostorg/math/pull/1078  

Using the `ibeta_inverse` function currently gives an unused variable warning at compile time since there is a variable that is only used in an assert:  
  
```cpp  
      const T x_extrema = 1 / (1 + a);  
      BOOST_MATH_ASSERT(0 < x_extrema && x_extrema < 1);  
```  
  
This can be avoided (and a few operations saved) by checking whether `a` is bounded by the values which would result in `x_extrema` failing the assert:  
  
```cpp  
      BOOST_MATH_ASSERT(  
         std::numeric_limits<T>::epsilon() < a  
         && a < std::numeric_limits<T>::infinity());  
```

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-02-01 18:51:49 UTC  
> Url: https://github.com/boostorg/math/pull/1078#issuecomment-1922008108  

It will need to be `(boost::math::tools<T>::epsilon)()` to avoid the errors inside `assert` I think.

---

## Comment 2

> Username: andrjohns  
> Created_at: 2024-02-04 12:29:10 UTC  
> Url: https://github.com/boostorg/math/pull/1078#issuecomment-1925732701  

@jzmaddock apologies for the false starts, this should be ready for review now

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-02-04 17:32:25 UTC  
> Url: https://github.com/boostorg/math/pull/1078#issuecomment-1925849961  

Thanks @andrjohns - looks good to me, merging...

---
