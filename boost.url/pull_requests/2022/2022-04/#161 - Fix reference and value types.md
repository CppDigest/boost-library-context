# #161 Fix reference and value types [Merged]

> Username: alandefreitas  
> Created at: 2022-04-01 19:32:11 UTC  
> Updated at: 2022-06-08 13:37:49 UTC  
> Merged at: 2022-04-02 00:44:25 UTC  
> Closed at: 2022-04-02 00:44:26 UTC  
> Url: https://github.com/boostorg/url/pull/161  

Fix #158  
  
This PR implements the following changes in container types:  
  
- `segments_encoded_view`  
    - `reference` = `string_view`  
    - `value_type` = `string_view` -> Replace with `const_string`  
- `segments_encoded`  
    - `reference` = `string_view`  
    - `value_type` = `std::string` -> Replace with `const_string`  
- `segments_view`  
    - `reference` = `const_string`  
    - `value_type` = `const_string`  
- `segments`  
    - `reference` = `const_string`  
    - `value_type` = `const_string`  
- `params_encoded_view`  
    - `reference` = `query_param_view`(`string_view`)  
    - `value_type` = `query_param` (`std::string`) -> Replace with `query_param` (`const_string`)  
- `params_encoded`  
    - `reference` = `query_param_view`(`string_view`)  
    - `value_type` = `query_param_view`(`string_view`) -> Replace with `query_param` (`const_string`)  
- `params_view`  
    - `reference` = `params_view::value_type`(`const_string`) -> Replace with `query_param` (`const_string`)  
    - `value_type` = `params_view::value_type`(`const_string`) -> Replace with `query_param` (`const_string`)  
- `params`  
    - `reference` = `params::reference`(`const_string`) -> Replace with `query_param` (`const_string`)  
    - `value_type` = `params::reference`(`const_string`) -> Replace with `query_param` (`const_string`)

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 19:55:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929364081  

📁 include/boost/url/impl/const_string.hpp

```diff
 177 |+         if (s.empty())
 178 |+             s = "";
 179 |         std::memcpy(data_.buf_,
```

> Username: vinniefalco  
> Created_at: 2022-04-01 19:55:48 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840880471  

`else`?


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 19:56:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929365050  

📁 include/boost/url/impl/const_string.ipp

```diff
 112 | {
 113 |+     if (s.empty())
 114 |+         s = "";
```

> Username: vinniefalco  
> Created_at: 2022-04-01 19:56:57 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840881118  

wait, what is this?

> Username: alandefreitas  
> Created_at: 2022-04-01 20:04:16 UTC  
> Updated_at: 2022-04-01 20:04:17 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840885572  

We need to check if `s.empty()` (but we don't need the `s = "";`) because `s.data()` might be `nullptr` for an empty string. I'll push the changes when you're done reviewing.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 19:57:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929365375  

📁 include/boost/url/impl/const_string.hpp

```diff
 176 |     {
 177 |+         if (s.empty())
 178 |+             s = "";
```

> Username: vinniefalco  
> Created_at: 2022-04-01 19:57:22 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840881350  

why do this?  
  
why not `s = {};`?

> Username: alandefreitas  
> Created_at: 2022-04-01 20:06:21 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840886689  

`s = {}` was what caused the problem in the first place. `s.p_` becomes `nullptr`. Anyway, we don't need this line anymore.  
  
```cpp  
BOOST_CONSTEXPR basic_string_view() BOOST_NOEXCEPT: p_(), n_()  
    {  
    }  
```


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 19:58:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929366011  

📁 include/boost/url/detail/parts_base.hpp

```diff
  12 | 
  13 | #include <boost/url/error.hpp>
  14 |+ #include <boost/url/string_view.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-04-01 19:58:12 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840881782  

What's this for? The file doesn't use `string_view`

> Username: alandefreitas  
> Created_at: 2022-04-01 20:17:32 UTC  
> Updated_at: 2022-04-01 20:17:33 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840892903  

`pos_t` is defined in `boost/url/string_view.hpp`

> Username: vinniefalco  
> Created_at: 2022-04-01 20:21:56 UTC  
> Updated_at: 2022-04-01 20:21:57 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840895298  

maybe that should go in config.hpp? do we have both `pos_t` and `off_t`? We only need one.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 19:59:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929366811  

📁 include/boost/url/impl/params.hpp

```diff
 252 | assign(std::initializer_list<
 252 |-     value_type> init)
 253 |+     query_param_view> init)
```

> Username: vinniefalco  
> Created_at: 2022-04-01 19:59:12 UTC  
> Updated_at: 2022-04-01 19:59:13 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840882314  

but this should say `reference` not `query_param_view`


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 19:59:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929367222  

📁 include/boost/url/impl/params.hpp

```diff
 389 |     iterator before,
 389 |-     value_type const& v) ->
 390 |+     query_param_view const& v) ->
```

> Username: vinniefalco  
> Created_at: 2022-04-01 19:59:41 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840882589  

I think this needs to say `reference`, but I could be convinced otherwise


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 20:03:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929370572  

📁 include/boost/url/impl/params_encoded.hpp

```diff
 233 | operator=(std::initializer_list<
 233 |-     value_type> init)
 234 |+     reference> init)
```

> Username: vinniefalco  
> Created_at: 2022-04-01 20:03:27 UTC  
> Updated_at: 2022-04-01 20:03:28 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840885043  

See this makes sense, `reference`


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 20:04:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929371305  

📁 include/boost/url/impl/params_encoded.ipp

```diff
  29 |     if(r.nv > 0)
  30 |-         return value_type{
  30 |+         return reference{
```

> Username: vinniefalco  
> Created_at: 2022-04-01 20:04:29 UTC  
> Updated_at: 2022-04-01 20:04:42 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840885670  

you could leave out the word `reference` and just return the initializer list


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 20:05:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929371998  

📁 include/boost/url/impl/params_view.ipp

```diff
  21 |- params_view::
  22 |- value_type::
  23 |- ~value_type() noexcept = default;
```

> Username: vinniefalco  
> Created_at: 2022-04-01 20:05:18 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840886129  

The purpose of emitting these definitions in the DLL is to avoid having them emitted in the user's code

> Username: alandefreitas  
> Created_at: 2022-04-01 20:19:44 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840894113  

Sorry. What does that have to do with removing `params_view::value_type::...`?

> Username: vinniefalco  
> Created_at: 2022-04-01 20:25:54 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840897623  

I'm saying that whatever replaces this type, should emit semi-expensive special member definitions in the DLL and not the user's binary.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 20:07:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929374098  

📁 include/boost/url/params_view.hpp

```diff
  15 |- #include <boost/url/const_string.hpp>
  15 | #include <boost/url/detail/parts_base.hpp>
  16 |+ #include <boost/url/const_string.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-04-01 20:07:53 UTC  
> Updated_at: 2022-04-01 20:07:54 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840887475  

const_string.hpp is not needed?


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 20:22:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929385558  

📁 include/boost/url/impl/const_string.ipp

```diff
 119 |+                 std::memcpy(
 120 |+                     dest,
 121 |+                     s.data(),
```

> Username: vinniefalco  
> Created_at: 2022-04-01 20:22:36 UTC  
> Updated_at: 2022-04-01 20:22:45 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840895681  

oh yeah, `nullptr` is UB even if `size()==0`...


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 20:28:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929390772  

📁 include/boost/url/query_param.hpp

```diff
 136 |+     /** Constructor
 137 |+     */
 138 |+     template <class Allocator = std::allocator<char>>
```

> Username: vinniefalco  
> Created_at: 2022-04-01 20:28:52 UTC  
> Updated_at: 2022-04-01 20:28:53 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840899246  

huh.. This template parameter is not used


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 20:29:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929391480  

📁 include/boost/url/query_param.hpp

```diff
 149 |+     /** Constructor
 150 |+     */
 151 |+     template <class Allocator = std::allocator<char>>
```

> Username: vinniefalco  
> Created_at: 2022-04-01 20:29:45 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840899688  

I think this overload should accept `const_string::factory const&` instead of `Allocator const&`, we don't need a separate _Allocator_ overload. That's actually a pessimization since we will be constructing two different factories for the same allocator (one for the key and one for the value).


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 20:30:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929391766  

📁 include/boost/url/query_param.hpp

```diff
 190 |+ 
 191 |+     BOOST_URL_DECL
 192 |+     query_param(
```

> Username: vinniefalco  
> Created_at: 2022-04-01 20:30:07 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840899872  

yeah shouldn't we just make this public?

> Username: alandefreitas  
> Created_at: 2022-04-01 20:44:36 UTC  
> Updated_at: 2022-04-01 20:45:01 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840907609  

This overload  
  
```  
query_param(  
        char const* s,  
        std::size_t nk,  
        std::size_t nv,  
        const_string::factory const& a);  
```  
  
has no meaning to users. It's used by the params containers to create a query_param from a `raw_param`, which is an implementation detail.  
  
In the future, we could have some overload like:  
  
```  
query_param(  
        string_view k,  
        string_view v,  
        bool has_value,  
        const_string::factory const& a);  
```  
  
but there's no demand for that in the library or from users yet.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 23:10:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929479861  

📁 include/boost/url/detail/config.hpp

```diff
  88 | 
  89 |+ #ifndef BOOST_URL_DOCS
  90 |+ // this is private
```

> Username: vinniefalco  
> Created_at: 2022-04-01 23:10:37 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840965895  

self-explanatory, since it is in detail/config.hpp (and also protected with the macro)


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 23:11:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929480224  

📁 include/boost/url/detail/config.hpp

```diff
  15 | #include <boost/config/workaround.hpp>
  16 | #include <limits.h>
  17 |+ #include <stddef.h>
```

> Username: vinniefalco  
> Created_at: 2022-04-01 23:11:58 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840966264  

but can we avoid including another header?


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 23:12:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929480436  

📁 include/boost/url/detail/config.hpp

```diff
  90 |+ // this is private
  91 |+ using pos_t = size_t;
  92 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2022-04-01 23:12:42 UTC  
> Updated_at: 2022-04-01 23:12:43 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840966442  

This could end up being a different size depending on platform, no? Why can't we do `unsigned int`? or uhh some guaranteed 32-bit integer type?


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 23:19:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/161#pullrequestreview-929482442  

📁 include/boost/url/detail/parts_base.hpp

```diff
  11 | #define BOOST_URL_DETAIL_PARTS_BASE_HPP
  12 | 
  13 |+ #include <boost/url/detail/config.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-04-01 23:19:27 UTC  
> Url: https://github.com/boostorg/url/pull/161#discussion_r840968088  

detail files don't need to include config.hpp... you only need to include config.hpp as the first include of a public header


---
