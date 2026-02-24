# #661 refactor: added select_most_precise<T1, T2>::type [Open]

> Username: marco-langer  
> Created at: 2022-05-05 07:23:24 UTC  
> Updated at: 2022-06-26 12:46:13 UTC  
> Url: https://github.com/boostorg/gil/pull/661  

### Description  
  
This is a first draft to implement a compile-time selection of a most precise type as suggested in #363.  
  
It follows closely the solution offered by Boost.Geometry, with the following changes:  
  
- arity of just 2 (but variable arity can be added later)  
- modernized to C++11  
- improved error handling in cases where one cannot select one of both types  
  
Some pending questions:  
- `std::is_floating_point` does not support floating point types other than float, double and long double. Do we expect other floating point types?  
  
Any thoughts on this?  
  
### Tasklist  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2022-05-05 08:34:38 UTC  
> Url: https://github.com/boostorg/gil/pull/661#issuecomment-1118301995  

Shall we use [`std::common_type`](https://en.cppreference.com/w/cpp/types/common_type) that has the advantage of following the usual promotion rules and might be specialized by users to support more exotic types (fixed point, multi precision...)?  
  
I found [this MP11 example](https://www.boost.org/doc/libs/1_79_0/libs/mp11/doc/html/mp11.html#generating_test_cases) inspiring.

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-05-17 22:34:59 UTC  
> Updated_at: 2022-05-19 07:28:08 UTC  
> Url: https://github.com/boostorg/gil/pull/661#issuecomment-1129387862  

@marco-langer   
Boost.Geometry's version allows pairs of integers  
```  
using B = boost::geometry::select_most_precise<short, short>::type;  
```  
while your variant requires one of the two must be float-point. Is that intentional?  
  
@sdebionne  
>Shall we use [std::common_type](https://en.cppreference.com/w/cpp/types/common_type) that has the advantage of following the usual promotion rules ... ?  
  
Good question. It depends ~as~ if we want or expect this:  
  
```c++  
static_assert(std::is_same<decltype(short{} * short{}), int>::value);  
```  
  
instead of this:  
  
```c++  
using T = std::common_type<short, short>::type;  
static_assert(std::is_same<T, short>::value);  
```  
  
or, BTW,  instead of this:  
  
```c++  
using T = boost::geometry::select_most_precise<short, short>::type;  
static_assert(std::is_same<UT, short>::value);  
```

---

## Comment 3

> Username: marco-langer  
> Created_at: 2022-05-19 06:42:39 UTC  
> Updated_at: 2022-06-04 03:42:00 UTC  
> Url: https://github.com/boostorg/gil/pull/661#issuecomment-1131292159  

Yes, disallowing two integral types was intentional (and I forgot to mention it in my above enumeration). While implementing my solution I too came to the same conclusion as @sdebionne, that the Boost.Geometry solution is problematic with respect to missing promotion rules.  
  
While your example seems to be quite reasonable  
```C++  
using T = boost::geometry::select_most_precise<short, short>::type;  
static_assert(std::is_same<T, short>::value);  
```  
consider an example in which both types have the same size but different signedness  
```C++  
  using T1 = boost::geometry::select_most_precise<short, unsigned short>::type;  
  using T2 = boost::geometry::select_most_precise<unsigned short, short>::type;  
  
  static_assert(std::is_same<T1, short>::value);  
  static_assert(std::is_same<T2, unsigned short>::value);  
```  
This non-commutativity is either a.) just unintuitive or b.) a potential source of bugs. One can easily contrive examples in which there is a chance of signed integer overflow if calling this trait with the wrong order of template arguments.

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-05-19 07:39:19 UTC  
> Url: https://github.com/boostorg/gil/pull/661#issuecomment-1131347790  

> This non-commutativity is either a.) just unintuitive or b.) a potential source of bugs.  
  
A very good point.  
  
> While implementing my solution I too came to the same conclusion as @sdebionne, that the Boost.Geometry solution is problematic with respect to missing promotion rules.  
  
So, we are leaning towards the `std::common_type` and given the C++ rule   
  
```c++  
static_assert(std::is_same<decltype(short{} * short{}), int>::value);  
```  
  
we are agreeing on the following type selection:  
  
```c++  
static_assert  
(  
    std::is_same  
    <  
        std::common_type  
        <  
            decltype(short{} * short{}),  
            short  
        >::type,  
        int  
    >::value  
);  
```  
  
Am I getting it right?

---

## Comment 5

> Username: marco-langer  
> Created_at: 2022-06-04 03:43:42 UTC  
> Url: https://github.com/boostorg/gil/pull/661#issuecomment-1146518736  

> Am I getting it right?  
  
Maybe we should have a look at some concrete use cases of this trait before answering this question?

---

## Comment 6

> Username: mloskot  
> Created_at: 2022-06-06 18:18:14 UTC  
> Url: https://github.com/boostorg/gil/pull/661#issuecomment-1147744171  

@marco-langer  How about @sdebionne 's #204 ? n.b. it's also a follow-up to #363 discussion, so could be fixed/closed by this PR too.

---
