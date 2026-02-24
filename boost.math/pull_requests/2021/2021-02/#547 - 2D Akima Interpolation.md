# #547 2D Akima Interpolation [Closed]

> Username: mborland  
> Created at: 2021-02-21 17:54:07 UTC  
> Updated at: 2024-02-22 10:26:52 UTC  
> Closed at: 2024-02-22 10:26:52 UTC  
> Url: https://github.com/boostorg/math/pull/547  

As requested by @NAThompson in the wishlist. Currently just the interface shell so it will be a draft for a bit. It looked to me like this could get computationally expensive, but would be relatively easy to thread. Being able to design for threading up front is significantly easier than going back and adding it in later.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-02-21 22:47:36 UTC  
> Updated_at: 2021-02-22 03:08:34 UTC  
> Url: https://github.com/boostorg/math/pull/547#issuecomment-782942224  

@mborland : Highly recommend *against* threading; these interpolators are often called from a multithreaded context; e.g., try to render a surface z = f(x,y) where f(x,y) is this interpolator.  
  
And thanks! I'm super excited for this.  
  
I think if you go fully unstructured, as is stated in your comments, then you'll have to do a KD-tree search for lookup. But if you restrict to uniform grids you get O(1) lookup and if you restrict to rectilinear grids you get log(N) lookup. Which should be fast, in addition much easier than fully unstructured.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-02-22 14:10:56 UTC  
> Updated_at: 2021-02-22 14:15:25 UTC  
> Url: https://github.com/boostorg/math/pull/547#issuecomment-783402389  

Here's the interface I had imagined for the (uniform grid) 2D Akima interpolation:  
  
```cpp  
#ifndef BOOST_MATH_INTERPOLATORS_AKIMA_2D_UNIFORM_HPP  
#define BOOST_MATH_INTERPOLATORS_AKIMA_2D_UNIFORM_HPP  
  
namespace boost::math::interpolators {  
  
template<typename Matrix>  
class akima_2d_uniform {  
public:  
    using Real = Matrix::value_type;  
    // The data is expected to be listed in row-major order, so that data(0,0) corresponds to (some decision here)  
    akima_2d_uniform(Real origin_x, Real origin_y, Real dx, Real dy, Matrix&& data);  
  
    Real operator()(Real x, Real y) const;  
  
    std::array<Real,2> gradient(Real x, Real y) const;  
  
    Matrix hessian(Real x, Real y) const;  
};  
  
}  
#endif  
```  
  
Ostensibly the `Matrix` type should be compatible with both Eigen and Boost.ublas.  
  
@jzmaddock: Is that how you would do it?

---

## Comment 3

> Username: mborland  
> Created_at: 2021-02-22 14:25:58 UTC  
> Url: https://github.com/boostorg/math/pull/547#issuecomment-783412264  

Not adding threading support in the first place is easy if it is unnecessary. [If you scroll down to appendix B](https://www.its.bldrdoc.gov/DownloadPublications.ashx?id=OT-75-70.pdf) you can see the authors original interface in FORTRAN. It is not too far off of what you are suggesting.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-02-22 15:05:15 UTC  
> Url: https://github.com/boostorg/math/pull/547#issuecomment-783438616  

> Not adding threading support in the first place is easy if it is unnecessary.  
  
I think threading the constructor would be a good idea if you had to assemble a KD-tree. But if you take on the uniform grid case first, there should be no issue.

---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2021-02-23 19:54:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/547#pullrequestreview-596740043  

📁 include/boost/math/interpolators/detail/2d_akima_detail.hpp

```diff
  25 |+ private:    
  26 |+     const Matrix data_;
  27 |+     const std::size_t n_interpolation_points_;
```

> Username: NAThompson  
> Created_at: 2021-02-23 19:54:13 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r581344131  

Wait, do I have to decide beforehand how many points I want to interpolate at?

> Username: mborland  
> Created_at: 2021-02-24 15:42:16 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582067887  

That was how the author's implementation works, but honestly I have not made it far enough to know if it's a hard requirement or not. I am implementing the steps of the procedure as I go down the paper as non-member functions so there is flexibility in interface. Same generally goes for your other comments.

> Username: NAThompson  
> Created_at: 2021-02-24 16:01:54 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582085209  

Yeah, that's a restriction of Fortran, not of this particular method. So your goal is to first do a faithful translation of the paper and the C++ it? (If so I'll shut up for a while.)

> Username: mborland  
> Created_at: 2021-02-24 16:14:40 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582095903  

He details a step by step procedure using equations that I am using to implement this.  There is a not so great scan copy of his code that is written in FORTRAN 66 which I would prefer not to translate. I am sure there are efficiencies that can be gained from the last 50 years.


---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2021-02-23 19:54:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/547#pullrequestreview-596740418  

📁 include/boost/math/interpolators/detail/2d_akima_detail.hpp

```diff
  20 |+ namespace boost { namespace math { namespace interpolators { namespace detail {
  21 |+ 
  22 |+ template <typename Matrix, typename VectorType = typename Matrix::value_type, typename Real = typename VectorType::value_type>
```

> Username: NAThompson  
> Created_at: 2021-02-23 19:54:41 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r581344390  

The `Matrix::value_type` for Eigen matrices will be (say) `float` or `double`, not a vector.


---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2021-02-23 19:55:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/547#pullrequestreview-596740921  

📁 include/boost/math/interpolators/detail/2d_akima_detail.hpp

```diff
  34 |+ 
  35 |+ public:
  36 |+     akima_2d_uniform_impl(Matrix&& data, std::size_t n) : data_{std::move(data)}, n_interpolation_points_{n}
```

> Username: NAThompson  
> Created_at: 2021-02-23 19:55:16 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r581344793  

So you can only do nxn data? I expect the most common case is in fact an nxm.


---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2021-02-23 19:56:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/547#pullrequestreview-596741712  

📁 test/test_2d_akima.cpp

```diff
  20 |+                            58.20,  4.73, 40.36, 13.62, 12.57,  8.74, 12.00, 14.81, 21.60, 26.50, 53.10, 49.43,  0.60,  5.52, 44.08};
  21 |+ 
  22 |+     std::vector<std::vector<double>> xyz {x, y, z};
```

> Username: NAThompson  
> Created_at: 2021-02-23 19:56:15 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r581345480  

A vector of vectors is a pretty bad data structure in general; why not an `Eigen` matrix?

> Username: mborland  
> Created_at: 2021-02-24 16:21:56 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582102012  

Is it worth supporting a vector of vectors or array of arrays? With some abuse of `std::conditional` it can happen but I would need to build an adapter that would transverse an iterator across the vectors to mimic the behavior of say ublas iterators.

> Username: NAThompson  
> Created_at: 2021-02-24 17:06:35 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582139119  

I would say it is not worth supporting vector of vectors. Array of arrays is more debatable because the storage is contiguous, but the syntax will be incompatible with all matrix classes, so I'd say don't try supporting array of arrays.

> Username: jzmaddock  
> Created_at: 2021-02-24 18:13:54 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582189664  

Just lurking here.... but the thing to do is decide on a matrix *concept*, and then use traits classes etc to map disparate concrete types (uBlas, Eigen, contain of container etc) to something that your code can understand.  
  
Back to lurking...

> Username: NAThompson  
> Created_at: 2021-02-24 18:15:24 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582190710  

@jzmaddock : Please lurk more!  
  
But are you saying both a vector of vectors and an Eigen matrix can work at the same time?

> Username: jzmaddock  
> Created_at: 2021-02-24 18:20:35 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582194420  

In the sense that one should be capable of being mapped to the other via an abstract interface/traits class.  I wouldn't see it as necessary to support *everything* from the get go though.  We should also try to have one eye on whatever might come along in the future, I'm somewhat surprised that there's still no std lib matrix/array support.

> Username: NAThompson  
> Created_at: 2021-02-24 18:45:56 UTC  
> Updated_at: 2021-04-11 06:49:39 UTC  
> Url: https://github.com/boostorg/math/pull/547#discussion_r582212475  

The matrix has been a sore point since you can't do:  
  
```  
operator[](int64_t i, int64_t j) const;  
```  
  
and write `M[i,j]`. People I guess want this before the matrix class can become a real boy.


---
