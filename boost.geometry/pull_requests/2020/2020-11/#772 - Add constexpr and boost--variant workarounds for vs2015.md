# #772 Add constexpr and boost::variant workarounds for vs2015. [Merged]

> Username: awulkiew  
> Created at: 2020-11-07 22:43:11 UTC  
> Updated at: 2020-11-26 18:24:32 UTC  
> Merged at: 2020-11-23 01:50:50 UTC  
> Closed at: 2020-11-23 01:50:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/772  

If you see our regression tests it looks like the majority of them compiles with VS2015:  
https://www.boost.org/development/tests/develop/developer/geometry.html  
  
Therefore I checked how much effort would it take to make all of them to compile. It seems that it was not much. VS2015 doesn't have full `constexpr` support and `boost::variant`'s is implemented using dummy template parameters instead of parameter pack.  
  
For `constexpr` it was sufficient to disable it in some places and add explicitly initialize class member in other.  
  
In order to analyze types of `boost::variant` I decided to not use Boost.MPL as it was before but instead to check raw template parameters, i.e. to check if they are geometries or not. This works for both dummy parameters and types that are not geometries. And while currently non-geometries are not supported in other places of the library we could consider supporting them. An example would be something like:  
```  
boost::variant<point, polygon, std::string> v = polygon{{{0, 0}, {0, 1}, {1, 1}, {1, 0}, {0, 0}}};  
auto a = area(v);  
```  
Right now this code would not compile. At least I think it would not. But I don't see a semantic reason why it shouldn't. So in the future we could consider expanding the change done in this PR to the whole library.  
  
What do you think about these workarounds?  
Does it make sense to support vs2015?

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-11-13 14:46:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/772#pullrequestreview-530116930  

I do not have a strong opinion against supporting VS2015, so I am ok with this patch.

📁 include/boost/geometry/algorithms/area_result.hpp

```diff
  77 | 
  78 |+ // Workaround for VS2015
  79 |+ #if (_MSC_VER < 1910)
```

> Username: vissarion  
> Created_at: 2020-11-13 14:41:44 UTC  
> Updated_at: 2020-11-13 14:46:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/772#discussion_r522992655  

should it be explicitly `_MSC_VER == 1900` or there is hope that the workaround will also work with older than VS15 versions ?

> Username: awulkiew  
> Created_at: 2020-11-13 15:12:38 UTC  
> Updated_at: 2020-11-13 15:12:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/772#discussion_r523012921  

No, I don't think that the older VS will compile the library.  
  
This way the checks are symmetric, i.e. `< 1910` vs `>= 1910`.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-11-21 01:56:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/772#issuecomment-731488895  

@mloskot @barendgehrels Do you have anything against merging it?

---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2020-11-22 15:46:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/772#pullrequestreview-536074324  

Looks OK to me

---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-11-26 03:58:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/772#issuecomment-734059569  

The #ifdefs were disabling `constexpr` with other compilers incorrectly.  
I fixed it here: https://github.com/boostorg/geometry/commit/928603f32ea629f6586f43a2894e6293b908f838

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2020-11-26 18:24:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/772#issuecomment-734435110  

Yes, better, thanks.

---
