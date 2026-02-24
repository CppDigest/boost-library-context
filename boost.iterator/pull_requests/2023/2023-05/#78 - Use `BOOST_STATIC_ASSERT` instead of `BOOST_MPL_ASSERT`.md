# #78 Use `BOOST_STATIC_ASSERT` instead of `BOOST_MPL_ASSERT` [Merged]

> Username: Flamefire  
> Created at: 2023-05-06 16:34:38 UTC  
> Updated at: 2023-05-07 13:03:06 UTC  
> Merged at: 2023-05-07 12:23:16 UTC  
> Closed at: 2023-05-07 12:23:16 UTC  
> Url: https://github.com/boostorg/iterator/pull/78  

The MPL version is slower to compile and `BOOST_STATIC_ASSERT` is already used in some places. So unify that.  
  
This also fixes `Wzero-as-null-pointer-constant` warnings, see https://github.com/boostorg/mpl/pull/75

---

## Review 1 [Changes requested]

> Username: Lastique  
> Created_at: 2023-05-06 20:55:51 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/iterator/pull/78#pullrequestreview-1415816648  

📁 include/boost/iterator/detail/facade_iterator_category.hpp

```diff
 116 |+   : integral_constant<bool,
 117 |+       is_convertible<T,std::input_iterator_tag>::value ||
 118 |+       is_convertible<T,std::output_iterator_tag>::value
```

> Username: Lastique  
> Created_at: 2023-05-06 20:38:37 UTC  
> Updated_at: 2023-05-06 20:55:51 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#discussion_r1186743929  

This one isn't about static asserts, is it? If you are willing to convert conditions then this should probably be done in a separate PR and for all conditions, not just this one.  
  
Also, this change is a potential regression in compile times as it always instantiates `is_convertible` twice. You can use `disjunction` from Boost.TypeTraits instead.  
  
Also, `integral_constant` requires `#include <boost/type_traits/integral_constant.hpp>`, though it would be replaced with `#include <boost/type_traits/disjunction.hpp>` if you used `disjunction`.

> Username: Flamefire  
> Created_at: 2023-05-07 09:14:00 UTC  
> Updated_at: 2023-05-07 09:56:44 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#discussion_r1186814444  

~`mpl::or_` requires using `foo::type::value` instead of `foo::value` and this one was used in static asserts hence the change to avoid the inconsistency.~ (Seems to be mistaken) I omitted the include as availability of `is_convertible` implies `integral_constant`  
  
Anyway to keep this as small as possible I reverted this change, it's still in the history for the future if needed.

> Username: Lastique  
> Created_at: 2023-05-07 10:37:01 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#discussion_r1186826913  

> I omitted the include as availability of `is_convertible` implies `integral_constant`  
  
I assume no implicit includes. If the code uses a component directly, its header must be included.  
  
> Anyway to keep this as small as possible I reverted this change  
  
Ok, thanks.

---

📁 include/boost/iterator/detail/facade_iterator_category.hpp

```diff
  16 |- # include <boost/mpl/assert.hpp>
  16 |+ 
  17 |+ #include <boost/static_assert.hpp>
```

> Username: Lastique  
> Created_at: 2023-05-06 20:39:07 UTC  
> Updated_at: 2023-05-06 20:55:51 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#discussion_r1186743992  

Please, preserve the formatting style.

---

📁 include/boost/iterator/detail/facade_iterator_category.hpp

```diff
 142 |-     BOOST_MPL_ASSERT_NOT((
 143 |-         is_convertible<
 141 |+     BOOST_STATIC_ASSERT(
```

> Username: Lastique  
> Created_at: 2023-05-06 20:43:10 UTC  
> Updated_at: 2023-05-06 20:55:51 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#discussion_r1186744327  

Single parentheses requires variadic macros, and we are not requiring C++11. At least, not yet and not as part of this PR. Please keep the double parentheses. Here, and everywhere else where there is a comma in the condition.

> Username: Flamefire  
> Created_at: 2023-05-07 09:27:53 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#discussion_r1186816675  

It looks like they were only required for `BOOST_MPL_ASSERT_NOT` [as documented](https://www.boost.org/doc/libs/1_82_0/libs/mpl/doc/refmanual/assert.html) and CI passes even for GCC 4.4 using C++98. I also verified that on [compiler explorer](https://godbolt.org/z/5Erz9KEh5) So if you don't feel strongly about those parentheses I'd keep the single ones especially as the extra parens would appear in the static assert output (see the godbolt link above)

> Username: Lastique  
> Created_at: 2023-05-07 10:35:34 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#discussion_r1186826728  

gcc supports variadic macros in C++03 as a non-standard extension, but I would like not to rely on it. Please, keep the double parenthesis.

> Username: Flamefire  
> Created_at: 2023-05-07 11:21:23 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#discussion_r1186833917  

Ok, done


---

## Comment 2

> Username: Lastique  
> Created_at: 2023-05-07 12:23:25 UTC  
> Url: https://github.com/boostorg/iterator/pull/78#issuecomment-1537428570  

Thanks.

---
