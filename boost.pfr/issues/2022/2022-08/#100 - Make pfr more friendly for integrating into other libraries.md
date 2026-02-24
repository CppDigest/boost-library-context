# #100 - Make pfr more friendly for integrating into other libraries? [Closed]

> Username: denzor200  
> Created at: 2022-08-01 11:09:48 UTC  
> Updated at: 2024-10-17 09:14:09 UTC  
> Closed at: 2024-10-17 09:14:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/100  

**Description of issue**  
Now i'm working with Boost.Fusion extension for Boost.PFR, i decomposed it into 3 PR's and opened these PR's here: https://github.com/boostorg/fusion/pull/241, here https://github.com/boostorg/fusion/pull/242 and here https://github.com/boostorg/fusion/pull/243.  
Review is moving on, there is some wishes from reviewers(more on that later).  
First, I would like to talk about such a problem like 3 PR's is too much code and the review takes too long.  
I suggest to make pfr more adaptable and more friendly to adapt into other libraries(not only fusion).  
  
**Suggested solution**  
1. To implement two functions:  
 - `boost::pfr::view`  
 - `boost::pfr::view_fields`  
2. To implement `BOOST_PFR_REGISTRATE_REFLECTABLE` macro.  
3. To add `boost::pfr::is_reflectable` trait. Example of using trait:  
```  
struct A {};  
struct B {};  
struct C {};  
BOOST_PFR_REGISTRATE_REFLECTABLE(A)  
using boost::pfr::is_reflectable;  
is_reflectable<A>::value;                                       //< true  
is_reflectable<decltype(boost::pfr::view_fields(B{}))>::value;  //< true  
is_reflectable<C>::value;                                       //< false  
```  
4. To use `boost::pfr::is_reflectable` trait for create overloading of compare/io operators and specialization of `std::hash` function. Examples of using these overloads:  
```  
#include <boost/pfr/ops.hpp>  
  
template <class T>  
struct uniform_comparator_less {  
    bool operator()(const T& lhs, const T& rhs) const noexcept {  
        // If T has operator< or conversion operator then it is used.  
        return boost::pfr::view(lhs) < boost::pfr::view(rhs);  
    }  
};  
```  
```  
#include <boost/pfr/functions_for.hpp>  
  
struct pair_like {  
    int first;  
    short second;  
};  
  
BOOST_PFR_REGISTRATE_REFLECTABLE(pair_like)   // Added pair_like's specialization for is_reflectable trait.  
  
// ...  
  
assert(pair_like{1, 2} < pair_like{1, 3});  
```  
```  
#include <boost/pfr/io_fields.hpp>  
  
struct pair_like {  
    int first;  
    std::string second;  
};  
  
inline std::ostream& operator<<(std::ostream& os, const pair_like& x) {  
    return os <<  bost::pfr::view_fields(x);  
}  
```  
  
  
5. To declare `io`, `eq`, `ne`, `lt`, `gt`, `le`, `ge`, `hash_value`, `io_fields`, `eq_fields`, `ne_fields`, `lt_fields`, `gt_fields`, `le_fields`, `ge_fields`, `hash_fields`, `BOOST_PFR_FUNCTIONS_FOR` as deprecated, and to actualize https://www.boost.org/doc/libs/1_75_0/doc/html/boost_pfr/tutorial.html#boost_pfr.tutorial.three_ways_of_getting_operators.  
  
**Conclusion**  
If everything planned can be done, then the PFR will be adapted as easily as the `std::tuple`, it will be just one small PR.

---

## Comment 1

> Username: denzor200  
> Created at: 2022-08-01 11:39:03 UTC  
> Updated at: 2022-08-01 11:39:34 UTC  
> Url: https://github.com/boostorg/pfr/issues/100#issuecomment-1201086974  

What further prospects will we get if we implement this approach:  
1. To extend `is_reflectable` trait, to implement it's code for auto detect that user defined type is reflectable.  
  It discussed here: https://github.com/boostorg/pfr/issues/56 and reviewers from Fusion wish it.  
2. To give for PFR users a fallback, in case the automation fails:  
-`BOOST_PFR_FORCE_REFLECTABLE` macro (just alias for `BOOST_PFR_REGISTRATE_REFLECTABLE`)  
-`BOOST_PFR_FORCE_NON_REFLECTABLE` macro  
-`boost::pfr::force_reflectable` function (just alias for `boost::pfr::view_fields`)  
-`boost::pfr::force_non_reflectable` function  
```  
struct A {};  
struct B {};  
struct C {};  
BOOST_PFR_FORCE_REFLECTABLE(A)  
BOOST_PFR_FORCE_NON_REFLECTABLE(B)  
using boost::pfr::is_reflectable;  
using boost::pfr::force_reflectable;  
using boost::pfr::force_non_reflectable;  
is_reflectable<A>::value;                                     //< true  
is_reflectable<B>::value;                                     //< false  
is_reflectable<decltype(force_reflectable(C{}))>::value;      //< true  
is_reflectable<decltype(force_non_reflectable(C{}))>::value;  //< false  
```  
3. To move it all into separate `experimental` include section.

---

## Comment 2

> Username: apolukhin  
> Created at: 2022-08-05 06:42:46 UTC  
> Url: https://github.com/boostorg/pfr/issues/100#issuecomment-1206100663  

I'd appreciate help with all the above points

---

## Comment 3

> Username: denzor200  
> Created at: 2022-08-27 12:20:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/100#issuecomment-1229182436  

I wrote a draft code for this functionality. The whole PR with docs and tests will be in a nearest future. I suppose that these changes will be super clean and won't get stuck on review.  
  
**Draft**: https://godbolt.org/z/Y98xhajb8  
  
Not all of the planned was done, some points turned out to be impracticable (and inexpedient), I will describe in more detail about deviations from the plan in the PR.  
  
How can we use modified PFR to make other libraries extensions? Like these:  
**Boost Json** extension sample: https://godbolt.org/z/GYY8e9jMs  
**Boost Fusion** extension sample: https://godbolt.org/z/Y6713Ej4a  
**Boost Spirit** with Fusions extension sample: https://godbolt.org/z/9cYvea3b7  
  
Also, i want to say that the most difficult part of the future PR is the implementation of `pfr::view`. It is an wrap-object, that can be a reference to underlying value or can be an owner, can be forwarded and can be implicitly casted to its underlying type.  
I didn't find a solution in all C++ libraries that i know, so i just reinvented the wheel.   
**View**: https://godbolt.org/z/rYhdnv9MW  
I suppose that this wrap-object can slow down the review for us.

---

## Comment 4

> Username: denzor200  
> Created at: 2022-11-27 18:01:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/100#issuecomment-1328306380  

We decided to select yet more simple strategy for integrating. In the Fusion side PFR will be fallback, and will be selected when no other reflection not found for T.  
  
It can be solved using `pfr::is_implicitly_reflectable` trait, no need for difficult `pfr::view_t`.  
  
**Draft**: https://godbolt.org/z/P1481orTM  
  
The whole PR in progress, also sample for Fusion and Spirit. It all will be available in the nearest future.

---

## Comment 5

> Username: denzor200  
> Created at: 2022-11-27 18:12:21 UTC  
> Url: https://github.com/boostorg/pfr/issues/100#issuecomment-1328308788  

> We decided to select yet more simple strategy for integrating. In the Fusion side PFR will be fallback, and will be selected when no other reflection not found for T.  
>   
> It can be solved using `pfr::is_implicitly_reflectable` trait, no need for difficult `pfr::view_t`.  
>   
> **Draft**: https://godbolt.org/z/P1481orTM  
>   
> The whole PR in progress, also sample for Fusion and Spirit. It all will be available in the nearest future.  
  
We also need to apply patch on the Fusion side, described here: https://github.com/boostorg/fusion/issues/231

---

## Comment 6

> Username: denzor200  
> Created at: 2022-12-01 08:14:22 UTC  
> Url: https://github.com/boostorg/pfr/issues/100#issuecomment-1333378009  

> We decided to select yet more simple strategy for integrating. In the Fusion side PFR will be fallback, and will be selected when no other reflection not found for T.  
>   
> It can be solved using `pfr::is_implicitly_reflectable` trait, no need for difficult `pfr::view_t`.  
>   
> **Draft**: https://godbolt.org/z/P1481orTM  
>   
> The whole PR in progress, also sample for Fusion and Spirit. It all will be available in the nearest future.  
  
Look at this: https://github.com/boostorg/pfr/pull/111

---

## Comment 7

> Username: apolukhin  
> Created at: 2024-10-17 09:14:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/100#issuecomment-2418996517  

Looks like everything is done here. Closing. Please create new issue if there's anything to improve
