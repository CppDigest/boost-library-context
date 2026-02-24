# #1077 - gauss_kronrod does not allow for non-literal types [Closed]

> Username: SteveBronder  
> Created at: 2024-01-31 17:59:46 UTC  
> Updated at: 2024-02-09 12:28:24 UTC  
> Closed at: 2024-02-09 12:28:24 UTC  
> Url: https://github.com/boostorg/math/issues/1077  

Using `gauss_kronrod` with a non-literal type causes a compiler error.  
  
```  
lib/boost_1.81.0/boost/math/quadrature/gauss_kronrod.hpp:139:41: error: the type ‘const std::array<stan::math::var_value<double>, 8>’ of ‘constexpr’ variable ‘data’ is not literal  
  139 |       static constexpr std::array<T, 8> data = {  
```  
  
Example godbolt here  
  
https://godbolt.org/z/K4Mxa85Mz  
  
I think a fix would be to detect if the type is literal for the gauss kronrod detail and have one implimentation that uses static constexpr arrays and the other uses arrays constructed at run time

---

## Comment 1

> Username: NAThompson  
> Created at: 2024-01-31 21:53:23 UTC  
> Url: https://github.com/boostorg/math/issues/1077#issuecomment-1920038727  

@SteveBronder : Just to contextualize this for me: What is the goal of the `stan::math::var_value<double>` type? Also, why can't it be a literal type?

---

## Comment 2

> Username: SteveBronder  
> Created at: 2024-01-31 22:32:01 UTC  
> Updated at: 2024-01-31 22:32:39 UTC  
> Url: https://github.com/boostorg/math/issues/1077#issuecomment-1920095539  

It's a scalar autodiff type used in the stan math library. Looking at the definitions for [LiteralType](https://en.cppreference.com/w/cpp/named_req/LiteralType) it fails   
  
> a type with at least one constexpr (possibly template) constructor that is not a copy or move constructor,  
  
The underlying memory for the var is made on a runtime arena allocator  
  
I think for our purposes if `gauss_kronrod` allowed you to choose types for the weights/abscicca and a seperate type for the given functions return type then it would work for us and also fix the problem

---

## Comment 3

> Username: NAThompson  
> Created at: 2024-01-31 22:59:30 UTC  
> Url: https://github.com/boostorg/math/issues/1077#issuecomment-1920134610  

> a seperate type for the given functions return type  
  
We do have a test for different return types:   
  
https://github.com/boostorg/math/blob/ca29a700f1ff7d508004a8843b07457e505b8b94/test/gauss_kronrod_quadrature_test.cpp#L454  
  
But the arena allocator . . .  that seems more difficult to support.  
  
@jzmaddock : Any ideas of how to support this?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2024-02-01 09:39:56 UTC  
> Url: https://github.com/boostorg/math/issues/1077#issuecomment-1920906385  

> Any ideas of how to support this?  
  
Maybe, I think some experimentation might be needed though...

---

## Comment 5

> Username: SteveBronder  
> Created at: 2024-02-01 14:58:18 UTC  
> Url: https://github.com/boostorg/math/issues/1077#issuecomment-1921526610  

> But the arena allocator . . . that seems more difficult to support  
  
The arena allocator is at global scope so it's not an issue.   
  
I think just making a gauss kronrod detail that has something like `std::enable_if_t<is_literal<T>::value>` and another with the opposite would fix it

---

## Comment 6

> Username: jzmaddock  
> Created at: 2024-02-01 17:30:43 UTC  
> Url: https://github.com/boostorg/math/issues/1077#issuecomment-1921847635  

> I think just making a gauss kronrod detail that has something like std::enable_if_t<is_literal<T>::value> and another with the opposite would fix it  
  
That involves a huge amount of duplication: I have a simpler solution working here, but I need to see if I can reduce bloat even further...
