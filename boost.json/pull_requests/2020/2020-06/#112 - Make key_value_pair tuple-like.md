# #112 Make key_value_pair tuple-like [Closed]

> Username: AeroStun  
> Created at: 2020-06-14 01:35:57 UTC  
> Updated at: 2020-07-01 07:40:45 UTC  
> Closed at: 2020-07-01 02:10:28 UTC  
> Url: https://github.com/boostorg/json/pull/112  

Fixes  #111   
Reviews and other comments are welcome

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-06-14 01:41:21 UTC  
> Url: https://github.com/boostorg/json/pull/112#issuecomment-643705612  

An automated preview of the documentation is available at [http://112.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://112.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-06-14 02:46:03 UTC  
> Updated_at: 2020-06-30 06:29:59 UTC  
> Url: https://github.com/boostorg/json/pull/112#issuecomment-643709658  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/112?src=pr&el=h1) Report  
> Merging [#112](https://codecov.io/gh/CPPAlliance/json/pull/112?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/f47fbeb1a54b832221a35c17912febb7a2c3a317&el=desc) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/112/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/112?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #112   +/-   ##  
========================================  
  Coverage    99.15%   99.15%             
========================================  
  Files           60       60             
  Lines         5217     5221    +4       
========================================  
+ Hits          5173     5177    +4       
  Misses          44       44             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/112?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/112/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/112?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/112?src=pr&el=footer). Last update [f47fbeb...3c7e026](https://codecov.io/gh/CPPAlliance/json/pull/112?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 3 [Commented]

> Username: madmongo1  
> Created_at: 2020-06-14 15:58:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/112#pullrequestreview-430231116  

2 comments

📁 test/value.cpp

```diff
1590 |+             get<1>(v2).get_string() == "value");
1591 |+ 
1592 |+ #if __cplusplus >= 201703L
```

> Username: madmongo1  
> Created_at: 2020-06-14 15:57:36 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439843553  

This won't work on MSVC, try _MSVC_LANG instead, checking for _MSC_VER first

> Username: AeroStun  
> Created_at: 2020-06-14 16:13:10 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439844948  

Good to know! Thanks for pointing it out; I believe I have now fixed that


📁 include/boost/json/value.hpp

```diff
2923 |+ */
2924 |+ template <>
2925 |+ struct tuple_element<1, ::boost::json::key_value_pair const> {
```

> Username: madmongo1  
> Created_at: 2020-06-14 15:57:53 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439843583  

No r-value overloads  
  
e.g.  
  
`std::get<1>(std::move(x));` should yield an r-value when x is mutable.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-14 16:38:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/112#pullrequestreview-430234009  

📁 test/value.cpp

```diff
1592 |- #if __cplusplus >= 201703L
1592 |+ #if __cplusplus >= 201703L || (defined(_MSVC_LANG) && _MSVC_LANG >= 201703L)
1593 |         auto const [kc, vc] = v1;
```

> Username: vinniefalco  
> Created_at: 2020-06-14 16:38:01 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439847037  

does `vc` receive a copy of the `json::value`?

> Username: AeroStun  
> Created_at: 2020-06-14 16:41:02 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439847270  

Correct; is it not a desired behavior?

> Username: vinniefalco  
> Created_at: 2020-06-14 17:26:19 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439850923  

I haven't used structured bindings, so I don't know. What if you want a reference? do you write  `auto const& [kc, kv] = v1`? Or do you write `auto const [kc, &kv] = v1]` ?

> Username: AeroStun  
> Created_at: 2020-06-14 17:34:28 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439851563  

`auto const& [kc, kv]`  
See the various `static_assert`s in the added test code  
I'm still struggling to get rvalues to work tho; I've actually fixed Richard's second comment, but didn't close it yet because I now want `auto [k, v] = kvp_prvalue` to be well formed and move the value, and likewise with `auto&& [krr, vrr] = kvp_prvalue`  
Been staring at [\[dcl.struct.bind\]#4](https://eel.is/c++draft/dcl.struct.bind#4) and I'm still unsure as to why I can't get both lvalues and rvalues to work without copying when `auto&&` is used.

> Username: vinniefalco  
> Created_at: 2020-06-14 17:44:11 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439852334  

`auto const& [kc, kv]` is interesting though, do you get a reference to a temporary `string_view`?

> Username: AeroStun  
> Created_at: 2020-06-14 17:46:02 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439852532  

No; the `string_view` is copied  
```cpp  
auto const& [kcr, vcr] = v1;  
static_assert(std::is_same_v<decltype(kcr), string_view const>);  
static_assert(std::is_same_v<decltype(vcr), value const&>);  
```


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-14 19:09:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/112#pullrequestreview-430243778  

📁 include/boost/json/value.hpp

```diff
2863 |+ >::type
2864 |+ {
2865 |+   BOOST_STATIC_ASSERT(I == -1u);
```

> Username: vinniefalco  
> Created_at: 2020-06-14 19:09:12 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r439858960  

nit: we use 4 spaces per tab


---

## Review 6 [Commented]

> Username: AeroStun  
> Created_at: 2020-06-29 15:38:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/112#pullrequestreview-439269105  

📁 test/value_from.cpp

```diff
 193 |+             BOOST_TEST(c.is_array());
 194 |+             BOOST_TEST(to_string(c) == to_string(b));
 195 |+         }
```

> Username: AeroStun  
> Created_at: 2020-06-29 15:38:32 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r447065957  

@sdkrystian pointed out to me that `value_from` has special support for tuple-like types, hence I added this  
However I am not a 100% sure whether `value_from(key_value_pair)` should yield an object with a single field, or an array with two elements (the current behavior).

> Username: sdkrystian  
> Created_at: 2020-06-29 17:44:42 UTC  
> Updated_at: 2020-06-30 00:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r447144227  

Two element array is correct.


---

## Comment 7

> Username: AeroStun  
> Created_at: 2020-06-30 00:01:28 UTC  
> Url: https://github.com/boostorg/json/pull/112#issuecomment-651431400  

(squashed)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2020-06-30 00:06:29 UTC  
> Url: https://github.com/boostorg/json/pull/112#issuecomment-651433155  

An automated preview of the documentation is available at [http://112.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://112.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-30 20:14:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/112#pullrequestreview-440351741  

📁 include/boost/json/value.hpp

```diff
2835 |+ template <std::size_t I>
2836 |+ auto
2837 |+ get(key_value_pair const&) noexcept ->
```

> Username: vinniefalco  
> Created_at: 2020-06-30 20:14:13 UTC  
> Url: https://github.com/boostorg/json/pull/112#discussion_r447951885  

These are missing Javadocs. I think it would be best to ifdef all of this so it is invisible to the documentation tool, and put a generic declaration in there just for the docs. Something like this:  
```  
#ifdef BOOST_JSON_DOCS  
  
/** Tuple-like interface  
*/  
template< std::size_t I, class T >  
__see_below__  
get( T&& );  
  
#else  
  
// actual defintions  
  
#endif  
```


---
