# #110 Refactor to const_string [Closed]

> Username: alandefreitas  
> Created at: 2022-01-10 21:15:27 UTC  
> Updated at: 2022-01-19 18:38:10 UTC  
> Closed at: 2022-01-12 20:19:42 UTC  
> Url: https://github.com/boostorg/url/pull/110  

This PR extends https://github.com/CPPAlliance/url/pull/107 to:  
  
- Refactor `string_value` to `const_string`  
- Make the new `const_string` reference counter atomic  
- Include basic javadoc for `const_string`  
- Fix a few quick quickbook warnings from the previous `string_value` javadoc

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:16:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848359545  

📁 include/boost/url/authority_view.hpp

```diff
 539 |-     string_value
 540 |-     userinfo(
 539 |+     const_string userinfo(
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:16:41 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781553759  

I put the type on its own line

> Username: alandefreitas  
> Created_at: 2022-01-10 21:18:36 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781554893  

OK. I'm on it. I'm going to miss clang-format on this PR. :laughing:


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:17:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848360518  

📁 include/boost/url/const_string.hpp

```diff
  13 |+ #include <boost/url/detail/config.hpp>
  14 |+ #include <boost/url/string_view.hpp>
  15 |+ #include <boost/type_traits/make_void.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:17:03 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781553969  

do we still need make_void?

> Username: alandefreitas  
> Created_at: 2022-01-10 21:18:50 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781554999  

I have no idea. I didn't touch that at all.

> Username: vinniefalco  
> Created_at: 2022-01-10 21:30:06 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781564700  

find out


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:17:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848360926  

📁 include/boost/url/const_string.hpp

```diff
  15 |+ #include <boost/type_traits/make_void.hpp>
  16 |+ #include <memory>
  17 |+ #include <string>
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:17:13 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781554056  

do we need <string> ?

> Username: alandefreitas  
> Created_at: 2022-01-10 21:40:30 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781574086  

I'll check


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:17:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848361687  

📁 include/boost/url/const_string.hpp

```diff
  21 |+ namespace urls {
  22 |+ 
  23 |+ /** A const string with shared ownership
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:17:33 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781554274  

"A non-modifable string with shared ownership"  
or  
"A constant string with shared ownership"


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:18:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848363521  

📁 include/boost/url/const_string.hpp

```diff
  96 |+ 
  97 |+ 
  98 |+      */
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:18:17 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781554665  

I'd prefer if we didn't have all this bulky commenting for private members, it just makes it harder to read


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:19:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848365272  

📁 include/boost/url/const_string.hpp

```diff
 116 |+ 
 117 |+      */
 118 |+     class allocator;
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:19:04 UTC  
> Updated_at: 2022-01-10 21:19:13 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781555138  

This can just be  
```  
#ifdef BOOST_URL_DOCS  
using allocator = __see_below__;  
#else  
class allocator;  
#endif  
without any javadoc

> Username: alandefreitas  
> Created_at: 2022-01-10 21:22:39 UTC  
> Updated_at: 2022-01-10 21:22:40 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781557680  

OK. Removing the javadoc then.

> Username: vinniefalco  
> Created_at: 2022-01-10 21:30:24 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781564966  

I think we might need something special for `allocator`


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:20:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848368323  

📁 include/boost/url/const_string.hpp

```diff
 132 |+         no string / nullptr.
 133 |+ 
 134 |+         @note As an optimization, the reference counter is `nullptr`.
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:20:15 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781555782  

We dont need this note and we don't need to say "default base string view".  
  
Instead, this can just read:  
  
```  
/** Constructor  
  
    Default-constructed strings are empty.  
*/


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:20:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848369943  

📁 include/boost/url/const_string.hpp

```diff
 137 |+     const_string() = default;
 138 |+ 
 139 |+     /** Construct an initial const_string
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:20:53 UTC  
> Updated_at: 2022-01-10 21:20:54 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781556160  

```  
/** Constructor  
```


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:21:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848370466  

📁 include/boost/url/const_string.hpp

```diff
 150 |+         @param n Number of chars in the string `dest`
 151 |+ 
 152 |+         @param a Allocator used to create a copy of `dest`
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:21:14 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781556389  

This doesn't actually make a copy, I think it just returns uninitialized memory and the caller has to fill it in. Please verify


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:21:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848370705  

📁 include/boost/url/const_string.hpp

```diff
 171 |+         @tparam Allocator Allocator type
 172 |+ 
 173 |+         @param s string_view whose contents will be copied to this const_string
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:21:31 UTC  
> Updated_at: 2022-01-10 21:21:32 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781556637  

```  
@param s The string to copy  
```


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:21:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848371000  

📁 include/boost/url/const_string.hpp

```diff
 178 |+     template< class Allocator =
 179 |+         std::allocator<char> >
 180 |+     explicit const_string(
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:21:54 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781556998  

it seems that the formatting has been changed

> Username: alandefreitas  
> Created_at: 2022-01-10 21:37:56 UTC  
> Updated_at: 2022-01-10 21:37:57 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781571815  

OK. Done.  
  
clang-format :cupid:


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:22:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848371380  

📁 include/boost/url/const_string.hpp

```diff
 188 |+         reference counter.
 189 |+ 
 190 |+         @param other The other const_string
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:22:22 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781557419  

```  
@param other The string to copy  
```


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:23:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848372243  

📁 include/boost/url/const_string.hpp

```diff
 198 |+ 
 199 |+         @note As @ref const_string is read-only, this function can only copy and increment
 200 |+         the string reference counter, which allows this operation to execute in constant time.
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:23:26 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781558407  

You don't need to say anything about being read-only or reference counting. Instead you could just wrote  
```  
/** Copy assignment  
  
    Objects of this type are cheap to copy.  
      
    @par Complexity  
    Constant time.  
    ...  
```


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-10 21:24:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/110#pullrequestreview-848372730  

📁 include/boost/url/impl/const_string.hpp

```diff
  25 |- auto
  26 |- string_value::
  26 |+ auto const_string::
```

> Username: vinniefalco  
> Created_at: 2022-01-10 21:24:02 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781558970  

The formatting changed everywhere

> Username: alandefreitas  
> Created_at: 2022-01-10 21:39:32 UTC  
> Updated_at: 2022-01-10 21:39:33 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781573299  

Yes. The IDE tried its best when refactoring the class name. I'll go through the project changing it.

> Username: vinniefalco  
> Created_at: 2022-01-10 21:57:01 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781591653  

You let the IDE do the rename?

> Username: alandefreitas  
> Created_at: 2022-01-10 22:14:11 UTC  
> Url: https://github.com/boostorg/url/pull/110#discussion_r781609793  

Partially, yes. It helped me locate the strings.  
  
I went through every change in the project. It should be OK now.


---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-01-10 21:24:28 UTC  
> Url: https://github.com/boostorg/url/pull/110#issuecomment-1009357562  

An automated preview of the documentation is available at [https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-01-10 21:54:30 UTC  
> Url: https://github.com/boostorg/url/pull/110#issuecomment-1009380296  

An automated preview of the documentation is available at [https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-01-10 21:59:35 UTC  
> Url: https://github.com/boostorg/url/pull/110#issuecomment-1009383436  

An automated preview of the documentation is available at [https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-01-10 22:14:29 UTC  
> Url: https://github.com/boostorg/url/pull/110#issuecomment-1009395218  

An automated preview of the documentation is available at [https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-01-10 22:19:28 UTC  
> Url: https://github.com/boostorg/url/pull/110#issuecomment-1009399166  

An automated preview of the documentation is available at [https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://110.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 20

> Username: codecov[bot]  
> Created_at: 2022-01-11 20:57:10 UTC  
> Url: https://github.com/boostorg/url/pull/110#issuecomment-1010353171  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/110?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#110](https://codecov.io/gh/CPPAlliance/url/pull/110?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (5451da9) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/756b6b6dbc896674620545423fd11df9b506d415?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (756b6b6) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/110/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/110?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #110   +/-   ##  
========================================  
  Coverage    96.38%   96.38%             
========================================  
  Files           85       85             
  Lines         5313     5313             
========================================  
  Hits          5121     5121             
  Misses         192      192             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/110?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaHBw) | `99.37% <ø> (ø)` | |  
| [include/boost/url/impl/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvY29uc3Rfc3RyaW5nLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9jb25zdF9zdHJpbmcuaHBw) | `94.31% <100.00%> (ø)` | |  
| [include/boost/url/impl/params.ipp](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaXBw) | `98.00% <100.00%> (ø)` | |  
| ... and [7 more](https://codecov.io/gh/CPPAlliance/url/pull/110/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/110?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/110?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [756b6b6...5451da9](https://codecov.io/gh/CPPAlliance/url/pull/110?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---
