# #319 - How does compute::copy() work? [Closed]

> Username: adamcavendish  
> Created at: 2014-11-29 06:57:00 UTC  
> Updated at: 2014-11-30 04:56:22 UTC  
> Closed at: 2014-11-30 04:56:22 UTC  
> Url: https://github.com/boostorg/compute/issues/319  

I'm reading examples and find out that Eigen::Matrix4f may actually assigned to compute::float16_.  
  
```  
std::vector<Eigen::Matrix4f> matrices(n);  
compute::vector<float16_> input(n, context);  
compute::copy(  
    matrices.begin(), matrices.end(), input.begin(), queue  
);  
```  
  
but if I do this it reports error, so I wonder whether it's the feature of compute::copy()  
  
```  
compute::float16_ mm = matrices[0];  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-11-29 07:19:57 UTC  
> Url: https://github.com/boostorg/compute/issues/319#issuecomment-64944097  

For that you can use the `eigen_matrix4f_to_float16()` function which is defined in the [`<boost/compute/interop/eigen/core.hpp>`](https://github.com/kylelutz/compute/blob/master/include/boost/compute/interop/eigen/core.hpp) header.  
  
For example, try:  
  
```  
compute::float16_ mm = compute::eigen_matrix4f_to_float16(matrices[0]);  
```

---

## Comment 2

> Username: adamcavendish  
> Created at: 2014-11-29 08:05:11 UTC  
> Url: https://github.com/boostorg/compute/issues/319#issuecomment-64944887  

I did not find that compute::copy() called compute::eigen_matrix4f_to_float16(). Where would it be ??

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-11-29 18:18:17 UTC  
> Url: https://github.com/boostorg/compute/issues/319#issuecomment-64960382  

Oh, `copy()` doesn't call `eigen_matrix4f_to_float16()`. That function is just convenient when converting between `Matrix4f` and `float16_` on the host. The `copy()` algorithm just copies the values and it works because `Matrix4f` and `float16_` have the same representation in memory (block of 16 `float`s).
