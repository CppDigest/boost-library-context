# #900 Fix for size_t underflow in tanh_sinh [Closed]

> Username: mborland  
> Created at: 2022-12-10 02:31:45 UTC  
> Updated at: 2022-12-11 14:55:59 UTC  
> Closed at: 2022-12-11 14:55:58 UTC  
> Url: https://github.com/boostorg/math/pull/900  

Closes #898

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2022-12-10 14:59:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/900#pullrequestreview-1212576534  

📁 test/git_issue_898.cpp

```diff
  22 |+     constexpr auto c1 = Real(2) * boost::math::constants::pi<Real>() * h * c * c ;
  23 |+     constexpr auto c2 = h * c / kB;
  24 |+     constexpr auto T = 1000.;
```

> Username: jzmaddock  
> Created_at: 2022-12-10 14:59:48 UTC  
> Url: https://github.com/boostorg/math/pull/900#discussion_r1045095543  

Interestingly, making T a double here *does not* reproduce the issue, making it an int:  
  
```  
constexpr auto T = 1000;  
```  
  
Not only reproduces the issue, but leads on to an out of bounds array subscript later on.  
  
BTW, I'm not convinced the suggested fix is quite correct: the purpose of `max_left_position` is to protect against evaluation at a singularity (or in this case, an infinite result).  This might need some careful thought...

> Username: mborland  
> Created_at: 2022-12-10 15:15:08 UTC  
> Url: https://github.com/boostorg/math/pull/900#discussion_r1045097216  

If `max_left_position` is 0 you would get underflow to `std::numeric_limits<size_t>::max()`, and then the array index is `max_left_position + 1` which overflows back to 0, but avoids seg fault.

> Username: jzmaddock  
> Created_at: 2022-12-10 16:52:54 UTC  
> Url: https://github.com/boostorg/math/pull/900#discussion_r1045107956  

Not here: https://github.com/boostorg/math/blob/61002717c6d49e7c93ac602a38470010cf80ae8d/include/boost/math/quadrature/detail/tanh_sinh_detail.hpp#L505  
  
There are assumptions all over the place that left/right_max_index are >0 but actually it should be permitted for *one* of them to go to zero if all the values on one half of the abscissa are either insignificant or incalculable.  
  
Let me think about this a bit more.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-12-11 13:13:39 UTC  
> Url: https://github.com/boostorg/math/pull/900#issuecomment-1345551635  

@mborland : I've pushed an alternate fix for this in https://github.com/boostorg/math/pull/901, if you have the time, can I get you to sanity check that I've covered all the situations where max_left_index/max_right_index could reach zero?  It is at least asserted now that they never do.

---

## Comment 3

> Username: mborland  
> Created_at: 2022-12-11 14:55:58 UTC  
> Url: https://github.com/boostorg/math/pull/900#issuecomment-1345575629  

Superseded by #901

---
