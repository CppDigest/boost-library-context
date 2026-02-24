# #1279 - polygamma function doc bug in reflection algorithm [Open]

> Username: tedgin  
> Created at: 2025-06-21 20:42:17 UTC  
> Updated at: 2025-06-23 00:26:17 UTC  
> Url: https://github.com/boostorg/math/issues/1279  

On the page describing the polygamma function (https://beta.boost.org/doc/libs/1_82_0/libs/math/doc/html/math_toolkit/sf_gamma/polygamma.html), in the Implementation section, there are two typos in the paragraph describing how to generate the coefficients of the terms of the cotangent derivatives. It should state that the iteration should start from <code>c<sub>0,1</sub>=-1</code>.  
  
Also, the derivative used to generate the subsequent coefficients should be in terms of <code>θ</code>, i.e., <code><sup>∂</sup>/<sub>∂θ</sub><sup>cos<sup>k</sup>θ</sup>/<sub>sin<sup>n</sup>θ</sub></code>.  
  
Edit: I think I found a third doc bug related to generation of these coefficients. This time, it is the the source: https://github.com/boostorg/math/blob/e1158bb6367a76accd493419567dc58ff6c47450/include/boost/math/special_functions/detail/polygamma.hpp#L280. I think there is an extra `- 1`, i.e., the formula should be `C[k+1, n+1] += (k - n) * C[k, n]`.
