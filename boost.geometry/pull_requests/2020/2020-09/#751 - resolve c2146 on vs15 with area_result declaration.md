# #751 resolve c2146 on vs15 with area_result declaration [Merged]

> Username: lpranam  
> Created at: 2020-09-05 16:54:22 UTC  
> Updated at: 2020-10-12 18:12:38 UTC  
> Merged at: 2020-10-12 18:12:38 UTC  
> Closed at: 2020-10-12 18:12:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/751  

Visual Studio 2015 C++14 (Default) we encountered C2146  stating  
`error C2146: syntax error: missing ',' before identifier 'result_type'`  
  
reference:   
https://ci.appveyor.com/project/lpranam/astronomy-il73i/branch/develop/job/uo7pirc0s4v9xpb2

---

## Comment 1

> Username: awulkiew  
> Created_at: 2020-09-05 22:36:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-687671647  

`typename` can't be used for base classes, see the failing minimal test:  
```  
./boost/geometry/algorithms/area_result.hpp:50:7: error: keyword ‘typename’ not allowed in this context (the base class is implicitly a type)  
```  
  
So the solution you proposed is incorrect. We could think of some workaround if we were sure that this is e.g. a bug in the compiler. But before that I'd like to investigate something different.  
  
Recently I modified the area() algorithm to use "umbrella strategies":  
https://github.com/boostorg/geometry/pull/726  
so strategies are used differently now.  
  
What Boost.Geometry headers do you include?  
How is `boost/geometry/algorithms/area_result.hpp` included in your code?  
Do you use `area`?  
Is `area_result` actually instantiated and if it is then where?  
Is the problem still there when you include the whole Boost.Geometry library?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-09-09 12:16:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-689523561  

@lpranam ^

---

## Comment 3

> Username: lpranam  
> Created_at: 2020-09-09 14:40:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-689607193  

@awulkiew I am yet to try your suggested solutions. Will update you soon :)

---

## Comment 4

> Username: lpranam  
> Created_at: 2020-09-12 08:38:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-691446530  

> So the solution you proposed is incorrect. We could think of some workaround if we were sure that this is e.g. a bug in the compiler. But before that I'd like to investigate something different.  
>   
> Recently I modified the area() algorithm to use "umbrella strategies":  
> #726  
> so strategies are used differently now.  
>   
> What Boost.Geometry headers do you include?  
  
Thee are all the headers we have used  
```  
<boost/geometry/core/cs.hpp>  
<boost/geometry/geometries/point.hpp>  
<boost/geometry/algorithms/transform.hpp>  
<boost/geometry/algorithms/equals.hpp>  
<boost/geometry/strategies/strategy_transform.hpp>  
<boost/geometry/arithmetic/cross_product.hpp>  
<boost/geometry/arithmetic/dot_product.hpp>  
```  
> How is `boost/geometry/algorithms/area_result.hpp` included in your code?  
> Do you use `area`?  
  
No we do not use `area` or the related header anywhere is the code.  
  
> Is `area_result` actually instantiated and if it is then where?  
  
No it is not instatiated  
  
> Is the problem still there when you include the whole Boost.Geometry library?  
  
yes, I tried including the entire library([commit](https://github.com/lpranam/astronomy/commit/740c71ecb819025278ef2df5844eda3aee34bfc3)) and the [result](https://ci.appveyor.com/project/lpranam/astronomy/build/job/ix0bpum3sj0fwf4t) is still the same  
  
@awulkiew ^

---

## Comment 5

> Username: gopi487krishna  
> Created_at: 2020-09-12 16:38:02 UTC  
> Updated_at: 2020-09-12 16:40:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-691514275  

A temporary solution in fact could be to provide an indirection for derivation. What i mean by that is using multilevel inheritance we can separate the typename qualification . A working example could be something like this  
  
![VS 2015](https://drive.google.com/uc?export=download&id=1yMs01l1NXS8YRShLFzpjy-q7RBh-zgHI)  
  
But as i said before this is just a temporary solution although it doesn't have any costs associated with it 🤔

---

## Comment 6

> Username: lpranam  
> Created_at: 2020-10-11 14:51:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-706716436  

@awulkiew a friendly ping. Do we have any solution for this?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2020-10-11 21:51:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-706773096  

@lpranam Not yet. The solution you proposed does not compile with standard-conforming compilers so I can't merge it. The solution proposed by @gopi487krishna looks overly complicated to me. If you want me to propose a workaround you'll have to wait because I have to test it with VS2015 which I don't have installed right now and I'm currently busy with something else.  
  
But maybe something like this would work. Could you try to compile it with VS2015?  
```  
template  
<  
    typename Geometry,  
    typename Strategy,  
    bool IsUmbrella = strategies::detail::is_umbrella_strategy<Strategy>::value  
>  
struct area_result  
{  
    typedef decltype(std::declval<Strategy>().area(std::declval<Geometry>())) strategy_type;  
    typedef typename strategy_type::template result_type<Geometry>::type type;  
};  
```

---

## Comment 8

> Username: lpranam  
> Created_at: 2020-10-12 18:06:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-707269057  

@awulkiew your proposed solution works. Though there seems to be some problem in CI which is unrelated to the code.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2020-10-12 18:12:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/751#issuecomment-707271641  

Ok, let's give it a try. Thanks!

---
