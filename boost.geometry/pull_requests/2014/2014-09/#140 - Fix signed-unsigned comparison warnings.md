# #140 [core] Fix signed/unsigned comparison warnings [Merged]

> Username: plopresti  
> Created at: 2014-09-22 21:16:15 UTC  
> Updated at: 2014-10-16 00:21:38 UTC  
> Merged at: 2014-09-24 10:46:57 UTC  
> Closed at: 2014-09-24 10:46:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/140  



---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-09-24 10:46:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/140#issuecomment-56653194  

Thanks for the patch!  
  
I'd like to add some additional assertions, e.g. `dimension<G>::value > 0`, `Dimensions > 0`, `dimension<G>::value < INT_MAX`, etc. They're probably not really needed but still...  
  
We could also consider replacing Dimensions type from int to e.g. `std::size_t` in those `assert_xxx()` functions. AFAIR Dimensions in algorithms implementations/dispatches are of type `std::size_t`, right? So here we could also use this type. Then we could only check `dimension<G>::value > 0`.  
  
AFAIS all `assert_dimension()` calls use hardcoded/constant `Dimensions`. `assert_dimension_less_equal()` isn't used at all. `assert_dimension_greater_equal()` is used 2x in matrix_transformer and also with hardcoded `Dimensions`. So replacing `int` with `size_t` should be ok.  
  
We could also replace `BOOST_STATIC_ASSERT` with `BOOST_MPL_ASSERT_MSG` for better error message.  
  
What do you think?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-09-24 10:56:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/140#issuecomment-56654002  

We could check `traits::dimension<...>::value > 0` in `bg::dimension<>`.  
  
Or even always convert the value to one type there no matter what the user had choosen in traits.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-09-24 11:42:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/140#issuecomment-56657816  

I think we should treat dimension as std::size_t everywhere in our code  
For user models, at compile time the Point Concept can check if dimension type is std::size_t  
This way we don't need to cast in assert_dimension

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-09-24 19:48:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/140#issuecomment-56727676  

Does it mean that you agree with my last proposal? Should `bg::dimension<G>::value` always be of type `std::size_t`? And AFAIU you propose that a value defined in `traits::dimension<>` should be converted to `std::size_t` in `core_dispatch::dimension<point_tag, P>`?  
  
What do you mean by "check if dimension type is std::size_t". If it wasn't then what should be done?  
  
If the dimension should always be of type `size_t` then is this check really needed? Wouldn't some asserts (`dimension >= 0` and if we wanted to be pedantic something like `dimension <= MAX_SIZE_T`) plus a `static_cast` be enough?

---

## Comment 5

> Username: plopresti  
> Created_at: 2014-09-24 21:17:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/140#issuecomment-56740256  

My two cents:  
  
Some people avoid unsigned integers in general, precisely because of their  
counter-intuitive behavior (e.g. "for (i=0 ; i < n-1 ; ++i)" when n==0).  
Other people think something like "size_t" encompasses exactly the expected  
values and therefore makes for a better interface. I personally lean  
towards the latter point of view, but I think both arguments have merit, so  
I do not argue with people either way. I just think it should be consistent  
in any given code base.  
  
Whichever option you choose, however, I suggest having a "safe_cast"  
mechanism that errors out on invalid conversions, especially if that can  
happen at compile time. (So if the user's dimension type is "int" and they  
pass -1, you error out rather than converting to ULONG_MAX. I believe this  
is what Barend is getting at.) It looks like numeric_cast from  
Boost.Numeric almost does this, except it appears to be run-time only (?).  
A compile-time equivalent would be pretty easy to write for just one type  
(e.g. size_t); if generic, it might make a nice addition to Boost.MPL.

---
