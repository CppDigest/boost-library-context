# #346 - Comparison between compile-time string and std::string behaves unexpectedly [Open]

> Username: ldionne  
> Created at: 2017-05-17 17:18:30 UTC  
> Updated at: 2020-12-24 11:40:01 UTC  
> Url: https://github.com/boostorg/hana/issues/346  

The following code has a very unexpected behavior:  
  
```c++  
#include <cassert>  
#include <string>  
  
#define BOOST_HANA_CONFIG_ENABLE_STRING_UDL  
#include <boost/hana/string.hpp>  
#include <boost/hana/equal.hpp>  
using namespace boost::hana::literals;  
using namespace std::literals;  
  
int main() {  
    assert(boost::hana::equal("x"_s, "x"s));  // assertion fails  
}  
```  
  
The problem here is that since `"x"_s` is a compile-time string (`hana::string`) and `"x"s` is a `std::string`, which have nothing in common (no common type, no conversion, etc.), Hana says they are not equal (instead of giving an error, for example). The rationale is documented in [`Comparable`'s documentation](http://boostorg.github.io/hana/group__group-Comparable.html):  
  
> In the context of programming with heterogeneous values, it is useful to have unrelated objects compare false instead of triggering an error. For this reason, equal adopts a special behavior for unrelated objects of tags `T` and `U` that do not satisfy the above requirements for the cross-type overloads. Specifically, when `T` and `U` are unrelated (i.e. `T` can't be converted to `U` and vice-versa), comparing objects with those tags yields a compile-time false value. This has the effect that unrelated objects like `float` and `std::string` will compare `false`, while comparing related objects that can not be safely embedded into the same super structure (like `long long` and `float` because of the precision loss) will trigger a compile-time assertion. Also note that for any tag `T` for which the minimal complete definition of `Comparable` is not provided, a compile-time assertion will also be triggered because `T` and `T` trivially share the common tag `T`, which is the expected behavior. This design choice aims to provide more flexibility for comparing objects, while still rejecting usage patterns that are most likely programming errors.  
  
This special behavior of comparing `false` for unrelated types usually works as intended, except when it does not. One _possible_ solution would be to check whether `T` and `U` are `Comparable`, and if so, then compare them when writing an algorithm. It _might_ make algorithms more complex to write, although one could provide a helper function that does that.  
  
Thanks to @Quuxplusone for finding this out.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-05-17 18:47:14 UTC  
> Updated at: 2017-05-17 18:47:33 UTC  
> Url: https://github.com/boostorg/hana/issues/346#issuecomment-302192692  

On a sort of related note, is it intended that `hana::Constant<T>` is not comparable with `T` if `T` is `hana::Comparable`?  
  
I have this:  
```cpp  
// Fails  
static_assert(boost::hana::equal(current_slide_t{42}, current_slide_c<42>));  
```

---

## Comment 2

> Username: ldionne  
> Created at: 2017-05-18 15:13:01 UTC  
> Url: https://github.com/boostorg/hana/issues/346#issuecomment-302434390  

You need a common type between the two, and the conversions have to be lossless.

---

## Comment 3

> Username: neel  
> Created at: 2020-12-24 11:40:01 UTC  
> Url: https://github.com/boostorg/hana/issues/346#issuecomment-750858050  

`hana::equal` may fail. But if there is an `operator==` between these two then it helps.
