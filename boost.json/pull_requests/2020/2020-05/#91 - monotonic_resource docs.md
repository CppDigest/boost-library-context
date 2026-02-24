# #91 monotonic_resource docs [Closed]

> Username: sdkrystian  
> Created at: 2020-05-14 02:04:15 UTC  
> Updated at: 2020-06-08 19:24:13 UTC  
> Closed at: 2020-05-20 00:01:31 UTC  
> Url: https://github.com/boostorg/json/pull/91  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-14 02:05:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/91#pullrequestreview-411416746  

📁 include/boost/json/monotonic_resource.hpp

```diff
  21 |+ /** A fast memory resource that never deallocates.
  22 |+     
  23 |+     `monotonic_resource` allocates large fixed-size
```

> Username: vinniefalco  
> Created_at: 2020-05-14 02:05:45 UTC  
> Updated_at: 2020-05-19 20:26:02 UTC  
> Url: https://github.com/boostorg/json/pull/91#discussion_r424830073  

Avoid repeating the type


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-14 02:06:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/91#pullrequestreview-411416855  

📁 include/boost/json/monotonic_resource.hpp

```diff
  27 |+     resource is destroyed; calls to @ref do_deallocate
  28 |+     have no effect. Memory will not be allocated unless
  29 |+     @ref do_allocate is called and the current block
```

> Username: vinniefalco  
> Created_at: 2020-05-14 02:06:06 UTC  
> Updated_at: 2020-05-19 20:26:02 UTC  
> Url: https://github.com/boostorg/json/pull/91#discussion_r424830169  

The user doesn't call `do_allocate` so this is confusing. The user calls `memory_resource::allocate`.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-14 02:06:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/91#pullrequestreview-411417132  

📁 include/boost/json/monotonic_resource.hpp

```diff
 192 |+ 
 193 |+         @throw std::bad_alloc Failure to allocate a new block.
 194 |+     */
```

> Username: vinniefalco  
> Created_at: 2020-05-14 02:06:59 UTC  
> Updated_at: 2020-05-19 20:26:02 UTC  
> Url: https://github.com/boostorg/json/pull/91#discussion_r424830371  

This is not going to work out well, we need to discuss it

> Username: sdkrystian  
> Created_at: 2020-05-15 20:33:08 UTC  
> Updated_at: 2020-05-19 20:26:02 UTC  
> Url: https://github.com/boostorg/json/pull/91#discussion_r426033404  

The way the exception is specified? Or is it because it doesn't directly throw the exception?  
  
I can just add a not saying that it calls `::operator new` and that might throw.

> Username: vinniefalco  
> Created_at: 2020-05-15 20:44:42 UTC  
> Updated_at: 2020-05-19 20:26:02 UTC  
> Url: https://github.com/boostorg/json/pull/91#discussion_r426038126  

Because of how it is derived from memory_resource - we talked about this already.

> Username: sdkrystian  
> Created_at: 2020-05-15 20:59:16 UTC  
> Updated_at: 2020-05-19 20:26:02 UTC  
> Url: https://github.com/boostorg/json/pull/91#discussion_r426044400  

Ah, we resolved that already then.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-14 02:13:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/91#pullrequestreview-411419018  

📁 include/boost/json/monotonic_resource.hpp

```diff
  44 |+ 
  45 |+     This memory resource is useful for parsing without
  46 |+     subsequent modification to the resulting #ref value.
```

> Username: vinniefalco  
> Created_at: 2020-05-14 02:13:08 UTC  
> Updated_at: 2020-05-19 20:26:02 UTC  
> Url: https://github.com/boostorg/json/pull/91#discussion_r424831940  

`@ref`


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-14 02:17:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/91#pullrequestreview-411420336  

📁 include/boost/json/monotonic_resource.hpp

```diff
  49 |     : public memory_resource
  50 | {   
  51 |+ #ifndef BOOST_JSON_DOCS
```

> Username: vinniefalco  
> Created_at: 2020-05-14 02:17:32 UTC  
> Updated_at: 2020-05-19 20:26:02 UTC  
> Url: https://github.com/boostorg/json/pull/91#discussion_r424833066  

shouldn't need this but check the docs


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2020-05-15 22:14:42 UTC  
> Updated_at: 2020-05-19 20:26:14 UTC  
> Url: https://github.com/boostorg/json/pull/91#issuecomment-629525256  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/91?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@e49288b`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/91/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/91?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #91   +/-   ##  
==========================================  
  Coverage           ?   99.03%             
==========================================  
  Files              ?       59             
  Lines              ?     5181             
  Branches           ?        0             
==========================================  
  Hits               ?     5131             
  Misses             ?       50             
  Partials           ?        0             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/91?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/monotonic\_resource.hpp](https://codecov.io/gh/CPPAlliance/json/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL21vbm90b25pY19yZXNvdXJjZS5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/91?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/91?src=pr&el=footer). Last update [e49288b...c002d04](https://codecov.io/gh/CPPAlliance/json/pull/91?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
