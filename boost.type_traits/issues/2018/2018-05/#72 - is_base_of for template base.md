# #72 - is_base_of for template base [Closed]

> Username: miralshah365  
> Created at: 2018-05-16 09:17:54 UTC  
> Updated at: 2019-09-16 17:48:10 UTC  
> Closed at: 2018-08-06 12:15:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/72  

`is_base_of` provides functionality to check whether a particular type is inherited from another particular type or not. But when the parent class is a templated class and want to check if the given type is inherited from any instance of the template it becomes hard to do it with `is_base_of`. Consider the example below:  
  
```  
template<typename X>  
class base{};  
  
class derived: public base<int>{};  
```  
  
now if I want to use `is_base_of` with this hierarchy it would look something like:  
  
`boost::is_base_of<base<X>, derived>::value;`  
  
and here X needs to be specified every time. But I would like to propose a way in which we don't need to specify the X and we can omit the base template argument and check general instance of the base.  
  
So if this seems good then I would like to submit the pull request for the same.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-05-16 17:01:32 UTC  
> Url: https://github.com/boostorg/type_traits/issues/72#issuecomment-389592580  

I'm having a hard time thinking of a use case for that - can you elaborate some more?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-06 12:15:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/72#issuecomment-410687898  

Closing as extra information not supplied.

---

## Comment 3

> Username: lpranam  
> Created at: 2019-09-16 17:48:10 UTC  
> Url: https://github.com/boostorg/type_traits/issues/72#issuecomment-531884100  

> I'm having a hard time thinking of a use case for that - can you elaborate some more?  
  
@jzmaddock this is a useful utility and I have used it in my GSoC project (`boost.astronomy`) too. A simplified example is as follows:  
  
Taking the standard example of shapes to demonstrate the inheritance but this time also providing g units with the measurements using `boost.units` and `boost.geometry`.  
  
```C++  
template <typename CoordinateSystem, typename CoordinateType>  
struct base{};  
  
template<  
    typename CoordinateType,  
    typename XQuantity,  
    typename YQuantity,  
    typename ZQuantity  
>  
struct cartesian: base<bg::cs::cartesian, CoordinateType>{};  
  
template<  
    typename CoordinateType,  
    typename LatQuantity,  
    typename LonQuantity,  
    typename DistQuantity  
>  
struct spherical: base<bg::cs::spherical<radian>, CoordinateType>{};  
  
template <typename Coordinate_1, typename Coordinate_2>  
Coordinate_1 sum(Coordinate_1 coordinate1, Coordinate_2 coordinate2)  
{  
    static_assert(is_base_template_of<base, Coordinate_1>::value, "Not a valid argument!");  
    static_assert(is_base_template_of<base, Coordinate_2>::value, "Not a valid argument!");  
    //code  
}  
```  
  
Here such utility comes handy because I don't care what type of units are used because they will be converted internally.  
  
More example can be found in [coordinate module](https://github.com/BoostGSoC19/astronomy/tree/gsoc19/include/boost/astronomy/coordinate) of `boost.astronomy`.  
  
If this seems nice then I can also send PR for the same.
