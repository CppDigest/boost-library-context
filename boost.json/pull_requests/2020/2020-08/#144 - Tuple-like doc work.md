# #144 Tuple-like doc work [Closed]

> Username: sdkrystian  
> Created at: 2020-08-10 21:48:56 UTC  
> Updated at: 2020-08-22 17:59:37 UTC  
> Closed at: 2020-08-12 00:25:40 UTC  
> Url: https://github.com/boostorg/json/pull/144  

Fixes #120

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-08-10 21:56:24 UTC  
> Url: https://github.com/boostorg/json/pull/144#issuecomment-671609881  

An automated preview of the documentation is available at [http://144.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://144.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 00:47:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464677716  

📁 include/boost/json/value.hpp

```diff
2886 |+ template<
2887 |+     class T,
2888 |+     std::size_t I>
```

> Username: vinniefalco  
> Created_at: 2020-08-11 00:47:02 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468263417  

Shouldn't the `I` come first?

> Username: sdkrystian  
> Created_at: 2020-08-11 18:09:13 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468769502  

Oh, oops


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 00:47:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464677871  

📁 include/boost/json/value.hpp

```diff
2876 |+ 
2877 |+     `T` is (possibly cv- or reference-qualified)
2878 |+     `key_value_pair`.
```

> Username: vinniefalco  
> Created_at: 2020-08-11 00:47:35 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468263546  

Use `@ref key_value_pair` and it will link

> Username: vinniefalco  
> Created_at: 2020-08-11 01:03:04 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468267532  

Actually the convention in the docs (I think) is to use a C++ expression here. So we might write something like:  
```  
@par Constraints  
  
`std::is_same< std::remove_cvref_t<T>, key_value_pair >`  
```


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 00:47:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464677914  

📁 include/boost/json/value.hpp

```diff
2881 |+     if `I` is `1`.
2882 |+ 
2883 |+     @param kvp The `key_value_pair` object
```

> Username: vinniefalco  
> Created_at: 2020-08-11 00:47:44 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468263572  

Use `@ref key_value_pair`


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 00:55:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464680100  

📁 include/boost/json/value.hpp

```diff
2838 |+     
2839 |+     This overload permits the key and value
2840 |+     of a `key_value_pair` to be accessed
```

> Username: vinniefalco  
> Created_at: 2020-08-11 00:55:31 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468265621  

```  
This function returns members of a @ref key_value_pair by index.  
```

> Username: sdkrystian  
> Created_at: 2020-08-11 18:13:34 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468771969  

Eh... I'd rather say "key and value". Members is too broad, and there's only two that we provide access to.

> Username: vinniefalco  
> Created_at: 2020-08-11 23:15:35 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468916074  

OK

> Username: vinniefalco  
> Created_at: 2020-08-11 23:16:10 UTC  
> Updated_at: 2020-08-11 23:16:11 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468916278  

The reason I worded it that way is to avoid repetition of the words "key" and "value" but we can go with your wording too.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 00:56:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464680383  

📁 include/boost/json/value.hpp

```diff
2846 |+     
2847 |+     auto key = get<0>(kvp);
2848 |+     auto value = get<1>(kvp);
```

> Username: vinniefalco  
> Created_at: 2020-08-11 00:56:39 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468265867  

We shouldn't use auto in docs, as there is no benefit to doing so and because showing the actual type is more informative for the reader:  
```  
string_view key = get<0>(kvp);  
value& jv = get<1>(kvp);


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 00:57:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464680740  

📁 include/boost/json/value.hpp

```diff
2852 |+     In addition to calling the function directly,
2853 |+     this function allows `key_value_pair` objects
2854 |+     to initialize structured bindings:
```

> Username: vinniefalco  
> Created_at: 2020-08-11 00:57:54 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468266198  

```  
@par Structured Bindings  
  
When using C++17 or greater, objects of type  
@ref key_value_pair may be used to initialize  
structured bindings:  
```


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 01:00:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464681553  

📁 include/boost/json/value.hpp

```diff
2869 |+     the argument is a non-const rvalue, and 
2870 |+     `const value&` otherwise..
2871 |+     Any other value for `I` is ill-formed.
```

> Username: vinniefalco  
> Created_at: 2020-08-11 01:00:45 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468266967  

```  
Depending on the value of `I`, the return type will be:  
  
@li `string_view const` if `I == 0`,  
  
@li `value&` or `value const&` if `I == 1`, otherwise  
  
@li the program is ill-formed.  
```  
  
Note that the docca toolchain has a preference for east-const, so we need to be consistent in user-facing program code and identifiers. We don't need to overly complicated the docs for "non-const lvalue" versus "non-const rvalue", this isn't the #standardese channel :)


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 01:07:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464683326  

📁 include/boost/json/value.hpp

```diff
2833 | //----------------------------------------------------------
2834 | 
2835 |+ #ifdef BOOST_JSON_DOCS
```

> Username: vinniefalco  
> Created_at: 2020-08-11 01:07:21 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468268634  

When we use `BOOST_JSON_DOCS` we need to strive to make the conditionally compiled section as small as possible. This exposes more of the doc material to the compiler and helps catch errors. For example we would prefer:  
```  
#ifdef BOOST_JSON_DOCS  
__see_below__  
#else  
ugly_type  
#endif  
great_function( complicated_args... );  
```  
  
over  
  
```  
#ifdef BOOST_JSON_DOCS  
__see_below__  
great_function( complicated_args... );  
#else  
ugly_type  
great_function( complicated_args... );  
#endif  
```  
  
So I think, we should try to apply this to the code here (not sure what the best way is).


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 01:07:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/144#pullrequestreview-464683478  

📁 include/boost/json/value.hpp

```diff
2858 |+     key_value_pair kvp("num", 42);
2859 |+ 
2860 |+     auto& [key, value] = kvp;
```

> Username: vinniefalco  
> Created_at: 2020-08-11 01:07:50 UTC  
> Updated_at: 2020-08-11 18:28:04 UTC  
> Url: https://github.com/boostorg/json/pull/144#discussion_r468268752  

Keep this as `auto` of course


---

## Comment 11

> Username: codecov[bot]  
> Created_at: 2020-08-11 18:13:47 UTC  
> Updated_at: 2020-08-11 19:42:20 UTC  
> Url: https://github.com/boostorg/json/pull/144#issuecomment-672153178  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/144?src=pr&el=h1) Report  
> Merging [#144](https://codecov.io/gh/CPPAlliance/json/pull/144?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/ec29dfa768a16672534f758005dfe4841a8ea93d&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/144/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/144?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #144   +/-   ##  
========================================  
  Coverage    98.34%   98.34%             
========================================  
  Files           63       63             
  Lines         5754     5754             
========================================  
  Hits          5659     5659             
  Misses          95       95             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/144?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/144/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/144?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/144?src=pr&el=footer). Last update [ec29dfa...50c061f](https://codecov.io/gh/CPPAlliance/json/pull/144?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2020-08-11 18:26:22 UTC  
> Url: https://github.com/boostorg/json/pull/144#issuecomment-672166424  

An automated preview of the documentation is available at [http://144.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://144.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2020-08-11 18:36:21 UTC  
> Url: https://github.com/boostorg/json/pull/144#issuecomment-672176916  

An automated preview of the documentation is available at [http://144.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://144.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2020-08-11 23:17:24 UTC  
> Url: https://github.com/boostorg/json/pull/144#issuecomment-672348629  

Approved

---
