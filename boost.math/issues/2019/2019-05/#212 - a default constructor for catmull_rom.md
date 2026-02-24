# #212 - a default constructor for catmull_rom? [Closed]

> Username: xarthurx  
> Created at: 2019-05-25 14:47:35 UTC  
> Updated at: 2019-05-27 21:28:18 UTC  
> Closed at: 2019-05-27 21:10:48 UTC  
> Url: https://github.com/boostorg/math/issues/212  

When trying to use this `catmull_rom` as a private member of a class, no matter what type `Point` is (say `Eigen::Vector2d>` here), there're always an error said   
`no default constructor exists for class boost::math::catmull_rom<Eigen::Vector2d>"`.  
  
This usually will not happen to other constructors.  
  
Would it be possible to provide an empty constructor for the class?

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-05-25 14:56:09 UTC  
> Updated at: 2019-05-25 15:06:18 UTC  
> Url: https://github.com/boostorg/math/issues/212#issuecomment-495924512  

No, there is no default constructor for the Catmull-Rom class.  
  
This is because the constructor computes the parametrization from the point set geometry. The class is therefore always in a valid state. A default constructor leaves the class in an invalid state.  
  
There's almost always a way around the problem you are encountering, though I do feel your pain!  
  
However, @jzmaddock has vastly more C++ knowledge than myself. John, do you know a clean way to solve this problem of creating a member variable out of a class with no default constructor? IIRC the way to do it is with a pointer member?  
  
(Your code reminds me I should add a unit test for `Eigen` vectors to the Catmull-Rom tests.)

---

## Comment 2

> Username: xarthurx  
> Created at: 2019-05-25 15:39:58 UTC  
> Url: https://github.com/boostorg/math/issues/212#issuecomment-495927577  

A pointer works, for the constructor.  
  
However, if the class is designed to be used this way, a method similar to `at()` that works the same as `operator()` should be provided, similar to what `std::vector` has.  
  
Otherwise, sth like `(*ptrToCatMullCrv)(t)` to get the value at `t` is really wired.

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-05-25 15:45:57 UTC  
> Url: https://github.com/boostorg/math/issues/212#issuecomment-495927982  

`at()` doesn't work that way: It does bounds checking on an integer index. It would definitely be surprising and non-idiomatic to use it in the way you are suggesting.  
  
Personally, I don't see any problem with `(*ptr)(t)`.

---

## Comment 4

> Username: xarthurx  
> Created at: 2019-05-27 21:28:18 UTC  
> Url: https://github.com/boostorg/math/issues/212#issuecomment-496308621  

OK, I'll use the `(*ptr)(t)` for now.  
But IMHO, I don't think this issue is solved yet...
