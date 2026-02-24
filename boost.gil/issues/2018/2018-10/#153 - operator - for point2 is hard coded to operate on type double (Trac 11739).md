# #153 - operator / for point2 is hard coded to operate on type double (Trac 11739) [Closed]

> Username: mloskot  
> Created at: 2018-10-13 19:48:16 UTC  
> Updated at: 2018-10-24 13:09:54 UTC  
> Closed at: 2018-10-24 13:08:40 UTC  
> Url: https://github.com/boostorg/gil/issues/153  

https://svn.boost.org/trac10/ticket/11739 description:  
  
> When using boost::gil::point2<float>, applying a division operation results in a point2<double>.  
> This is undesirable.  
> The fix appears to be easy, change this code  
>   
> ```  
> template <typename T> BOOST_FORCEINLINE  
> point2<double> operator/(const point2<T>& p, double t)      { return t==0 ? point2<double>(0,0):point2<double>(p.x/t,p.y/t); }  
> ```  
>   
> to operate on `T` instead of `double`.  
> But, that seems overly simple so I am trying to figure out why this code was written this way in the first place.  
>   
> A possibly safer alternative is to cast the result back to `point2<T>`, ie;  
>   
> ```  
> template <typename T> BOOST_FORCEINLINE  
> point2<double> operator/(const point2<T>& p, double t)      { return t==0 ? point2<T>(0,0):point2<T>(T(p.x/t),T(p.y/t)); }  
> ```  
>   
> This change makes the / operator act the same as the /= operator,  
> which is also hard coded to double, but because it's a member, doesn't yield a new type,  
> so it's probably fine (other than the fact that I asked for floats and am getting double operations).  
>   
> While we're at it, I also find it weird that this code is protecting against division by zero,  
> but that's a different problem.  
  
Let's discuss the proposed modification (/cc @chhenning & @stefanseefeld)

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-10-17 23:03:37 UTC  
> Url: https://github.com/boostorg/gil/issues/153#issuecomment-430819198  

What is the possible range (set of valid types) of `T` ? I think I have written similar code in the past, where my assumption was that the divisor must be a wide floating point value for the operation to make sense, which would explain the second `double` argument. And then it would make sense to use simple type promotion to compute the return type, which may just be `double`, too, in this case.  
(I'm just thinking aloud, trying to mentally reproduce why the code was written the way it is.)

---

## Comment 2

> Username: christian-henning  
> Created at: 2018-10-18 13:04:17 UTC  
> Url: https://github.com/boostorg/gil/issues/153#issuecomment-431000214  

@mloskot Is there not a better point class in boost? Wouldn't that be better than using gil::point2?

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-10-18 13:14:00 UTC  
> Url: https://github.com/boostorg/gil/issues/153#issuecomment-431003374  

It's such a trivial class...  
I'm not sure it would be worthwhile dragging in another dependency just for that. Besides, there are rather specific requirements for it (See http://boostorg.github.io/gil/html/design_guide.html#point) that I'm not sure any pre-existing class would meet without additional adapter code.  
(And speaking of requirements: the `/=` signature using `double` actually comes from the concept itself.)

---

## Comment 4

> Username: mloskot  
> Created at: 2018-10-18 19:27:13 UTC  
> Url: https://github.com/boostorg/gil/issues/153#issuecomment-431132369  

I agree with @stefanseefeld that pulling Boost.Geometry or whatever as a dependency would not buy as anything really, but potential maintenance hassle.  
  
I will look at implementing the idea of promoting the return type to best float-point type matching the operands.

---

## Comment 5

> Username: mloskot  
> Created at: 2018-10-19 16:17:01 UTC  
> Updated at: 2018-10-19 16:31:26 UTC  
> Url: https://github.com/boostorg/gil/issues/153#issuecomment-431417759  

@stefanseefeld  & @chhenning   
How about replacing the current `double`-hardwired `point<T>::operator/` with template function auto-promoting the base `T` of its `point<T>` result using [std::common_type](https://en.cppreference.com/w/cpp/types/common_type) which I had no idea about until today :-)  
  
```cpp  
template <typename T, typename D>  
auto operator/(point<T> const& p, D d)  
{  
    static_assert(std::is_arithmetic<D>::value, "denominator is not arithmetic type");  
    using numeric_type = typename std::common_type<T, D>::type;  
    return point<numeric_type>(p.x / t, p.y / t);  
}  
```  
  
Test:  
  
```cpp  
point<int> p1;  
static_assert(std::is_same<decltype((p1 / short{}).x), int>::value, "!int");  
static_assert(std::is_same<decltype((p1 / int{}).x), int>::value, "!int");  
static_assert(std::is_same<decltype((p1 / float{}).x), float>::value, "!float");  
static_assert(std::is_same<decltype((p1 / double{}).x), double>::value, "!double");  
  
point<float> p2;  
static_assert(std::is_same<decltype((p2 / int{}).x), float>::value, "!float");  
static_assert(std::is_same<decltype((p2 / float{}).x), float>::value, "!float");  
static_assert(std::is_same<decltype((p2 / double{}).x), double>::value, "!double");  
  
```

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2018-10-19 17:27:34 UTC  
> Url: https://github.com/boostorg/gil/issues/153#issuecomment-431437672  

Yeah, perfect !

---

## Comment 7

> Username: mloskot  
> Created at: 2018-10-24 13:09:54 UTC  
> Url: https://github.com/boostorg/gil/issues/153#issuecomment-432649110  

Fixed by #157
