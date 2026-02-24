# #1 Add zero size optimization and use smallest avaliable type for size [Closed]

> Username: sdkrystian  
> Created at: 2019-11-29 02:39:53 UTC  
> Updated at: 2019-12-16 05:55:06 UTC  
> Closed at: 2019-12-16 05:52:46 UTC  
> Url: https://github.com/boostorg/static_string/pull/1  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2019-11-29 02:42:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/1#pullrequestreview-324500301  

📁 include/boost/fixed_string/detail/fixed_string.hpp

```diff
  36 |+ struct priority<0> { };
  37 |+ 
  38 |+ template<std::size_t N, std::enable_if_t<N < std::numeric_limits<unsigned char>::max()>* = nullptr>
```

> Username: vinniefalco  
> Created_at: 2019-11-29 02:42:36 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351963199  

is `std::enable_if_t` C++11?

> Username: sdkrystian  
> Created_at: 2019-11-29 02:43:39 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351963326  

@vinniefalco Nope, totally forgot about that


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2019-11-29 02:43:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/1#pullrequestreview-324500487  

📁 include/boost/fixed_string/detail/fixed_string.hpp

```diff
  39 |+ constexpr unsigned char smallest_width_impl(priority<3>);
  40 |+ 
  41 |+ template<std::size_t N, std::enable_if_t<N < std::numeric_limits<unsigned short>::max()>* = nullptr>
```

> Username: vinniefalco  
> Created_at: 2019-11-29 02:43:35 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351963319  

calls to `max` have to be protected against macros, e.g.  
```  
(std::numeric_limits<unsigned short>::max)()  
```  
  
Or you could just write `USHRT_MAX` and spare yourself all of the lines included by `<numeric>`.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2019-11-29 02:46:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/1#pullrequestreview-324500939  

📁 include/boost/fixed_string/detail/fixed_string.hpp

```diff
 104 |+   {
 105 |+     static CharT null{};
 106 |+     return &null;
```

> Username: vinniefalco  
> Created_at: 2019-11-29 02:46:02 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351963697  

Well this will make the address of null different. How about using const cast so you only have one `static CharT`?

> Username: sdkrystian  
> Created_at: 2019-11-29 02:47:43 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351963967  

That would make any use of data extremely dangerous.

> Username: vinniefalco  
> Created_at: 2019-11-29 02:50:36 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351964349  

Not really, because the caller is only allowed to observe the null not to change it. And "magic statics" are thread safe.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2019-11-29 02:46:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/1#pullrequestreview-324501045  

📁 include/boost/fixed_string/fixed_string.hpp

```diff
  39 |     typename Traits = std::char_traits<CharT>>
  40 |- class fixed_string
  40 |+ class fixed_string : public detail::fixed_string_base<N, CharT>
```

> Username: vinniefalco  
> Created_at: 2019-11-29 02:46:37 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351963784  

This needs to be a private derivation, and you need to protect it from the docs using `#ifndef GENERATING_DOCUMENTATI)ON` or whatever


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2019-11-29 02:47:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/1#pullrequestreview-324501278  

📁 include/boost/fixed_string/fixed_string.hpp

```diff
  44 | 
  45 |     void
  46 |     term()
```

> Username: vinniefalco  
> Created_at: 2019-11-29 02:47:52 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351963984  

I think you need `term_impl()` in the base, to specialize it away for N==0.


---

## Review 6 [Commented]

> Username: glenfe  
> Created_at: 2019-11-29 03:53:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/1#pullrequestreview-324511241  

📁 include/boost/fixed_string/detail/fixed_string.hpp

```diff
  55 |+ 
  56 |+ template<std::size_t N>
  57 |+ using smallest_width_t = typename smallest_width<N>::type;
```

> Username: glenfe  
> Created_at: 2019-11-29 03:46:28 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351971856  

Why not instead something like:  
```  
template<std::size_t N>  
using smallest_width_t =   
    typename std::conditional<N <= std::numeric_limits<unsigned char>::max(), unsigned char,  
    typename std::conditional<N <= std::numeric_limits<unsigned short>::max(), unsigned short,  
    typename std::conditional<N <= std::numeric_limits<unsigned>::max(), unsigned,  
    typename std::conditional<N <= std::numeric_limits<unsigned long>::max(), unsigned long,  
    typename std::conditional<N <= std::numeric_limits<unsigned long long>::max(), unsigned long long,  
    void>::type>::type>::type>::type>::type;   
```  
  
You could even replace the `void` above with `struct unsupported_range { };`

---

📁 include/boost/fixed_string/detail/fixed_string.hpp

```diff
  15 | #include <iterator>
  16 | #include <type_traits>
  17 |+ #include <numeric>
```

> Username: glenfe  
> Created_at: 2019-11-29 03:47:01 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351971955  

`#include <limits>`


---

## Review 7 [Commented]

> Username: glenfe  
> Created_at: 2019-11-29 04:42:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/1#pullrequestreview-324519669  

📁 include/boost/fixed_string/fixed_string.hpp

```diff
2617 |+ #ifndef BOOST_FIXED_STRING_STANDALONE
2618 |+   return boost::hash_range(str.begin(), str.end());
2619 |+ #else
```

> Username: glenfe  
> Created_at: 2019-11-29 04:42:08 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r351978926  

The outer `#ifndef` doesn't make sense if you have the inner one. i.e. How would it ever hit the inner `#else`?


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-04 11:43:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/1#pullrequestreview-326786135  

📁 include/boost/fixed_string/fixed_string.hpp

```diff
1417 |+     */
1418 |+     string_view_type
1419 |+     subview(
```

> Username: vinniefalco  
> Created_at: 2019-12-04 11:43:41 UTC  
> Updated_at: 2019-12-05 06:04:44 UTC  
> Url: https://github.com/boostorg/static_string/pull/1#discussion_r353695749  

Rather than introduce new syntax I think `string_view(fs).substr(...)` where `fs` is a fixed string is entirely reasonable.


---
