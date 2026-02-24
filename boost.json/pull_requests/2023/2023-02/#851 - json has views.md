# #851 json has views. [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-02-01 09:18:46 UTC  
> Updated at: 2024-10-04 11:12:20 UTC  
> Closed at: 2024-10-04 11:12:20 UTC  
> Url: https://github.com/boostorg/json/pull/851  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-02-01 09:19:45 UTC  
> Url: https://github.com/boostorg/json/pull/851#issuecomment-1411721864  

incomplete, but should shows the idea.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-02-01 10:34:47 UTC  
> Url: https://github.com/boostorg/json/pull/851#issuecomment-1411836508  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/851/pullrequest-condensed-8a8cee5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/851/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/851/pullrequest.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 10:56:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278884945  

📁 include/boost/json/detail/value_view.hpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2019 Vinnie Falco (vinnie.falco@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2023-02-01 10:56:43 UTC  
> Updated_at: 2023-02-01 10:56:44 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093065346  

Klemens your name goes here not mine


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 10:57:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278885848  

📁 include/boost/json/detail/value_view.hpp

```diff
  27 |+ struct scalar_view
  28 |+ {
  29 |+     kind k;         // must come first
```

> Username: vinniefalco  
> Created_at: 2023-02-01 10:57:20 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093065934  

Does this really have to come first?


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 10:58:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278887605  

📁 include/boost/json/impl/array_view.hpp

```diff
  19 |+ struct array_view::container_adaptor final : adaptor
  20 |+ {
  21 |+     virtual bool            empty(const void * data)   const override
```

> Username: vinniefalco  
> Created_at: 2023-02-01 10:58:29 UTC  
> Updated_at: 2023-02-01 10:58:30 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093067158  

why is this spaced all weird


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 10:59:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278889709  

📁 include/boost/json/impl/array_view.hpp

```diff
 121 |+         return *(static_cast<const T*>(data) - (Size - 1));
 122 |+     }
 123 |+     virtual boost::optional<value_view> if_contains(const void * data, std::size_t pos) const override
```

> Username: vinniefalco  
> Created_at: 2023-02-01 10:59:53 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093068575  

couldn't you just return a null view instead of a bulky optional?


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 11:01:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278891393  

📁 include/boost/json/impl/array_view.ipp

```diff
   9 |+ 
  10 |+ #ifndef BOOST_JSON_DETAIL_IMPL_ARRAY_VIE_HPP
  11 |+ #define BOOST_JSON_DETAIL_IMPL_ARRAY_VIE_HPP
```

> Username: vinniefalco  
> Created_at: 2023-02-01 11:01:02 UTC  
> Updated_at: 2023-02-01 11:01:03 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093069694  

spelling


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 11:01:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278892843  

📁 include/boost/json/object_view.hpp

```diff
  14 |+ #include <boost/json/kind.hpp>
  15 |+ #include <boost/optional.hpp>
  16 |+ #include <boost/describe/class.hpp>
```

> Username: vinniefalco  
> Created_at: 2023-02-01 11:01:58 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093070630  

json includes first, then boost, then std

> Username: vinniefalco  
> Created_at: 2023-02-01 11:02:23 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093071066  

doesn't this need to be ifdef for c++14 and later?


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 11:04:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278896374  

📁 include/boost/json/object_view.hpp

```diff
 153 |+ 
 154 |+     /// A const random access iterator to an element
 155 |+     struct const_iterator
```

> Username: vinniefalco  
> Created_at: 2023-02-01 11:04:20 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093072966  

you need to forward declare this here and put the declaration in json/impl/object_view.hpp


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 11:04:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278897066  

📁 include/boost/json/object_view.hpp

```diff
 278 |+     /** Destructor.
 279 |+ 
 280 |+         The destructor for each element is called if needed,
```

> Username: vinniefalco  
> Created_at: 2023-02-01 11:04:47 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093073423  

is this right though? no destructors are called


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 11:05:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278898354  

📁 include/boost/json/object_view.hpp

```diff
 367 |+                 typename std::enable_if<
 368 |+                        is_map_like<T>::value
 369 |+                     && (sizeof(typename T::const_iterator) == sizeof(void*))
```

> Username: vinniefalco  
> Created_at: 2023-02-01 11:05:38 UTC  
> Updated_at: 2023-02-01 11:05:39 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093074233  

OK we are going to need to enforce the rule, only one indent level at a time. Never indenting based on the length of an identifier.


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 11:06:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278898900  

📁 include/boost/json/object_view.hpp

```diff
 379 |+     /** Copy assignment.
 380 |+ 
 381 |+         The contents of the object are replaced with an
```

> Username: vinniefalco  
> Created_at: 2023-02-01 11:06:01 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093074609  

but this is not accurate


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 11:06:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278899169  

📁 include/boost/json/object_view.hpp

```diff
 411 |+         No-throw guarantee.
 412 |+     */
 413 |+     inline
```

> Username: vinniefalco  
> Created_at: 2023-02-01 11:06:12 UTC  
> Updated_at: 2023-02-01 11:06:13 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093074775  

you dont need `inline` here


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 11:45:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1278953901  

📁 include/boost/json/array_view.hpp

```diff
 253 |+ 
 254 |+     /// A reference to an element
 255 |+     using reference = value_view&;
```

> Username: vinniefalco  
> Created_at: 2023-02-01 11:45:19 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093112833  

These can't be assigned though, so this should be const?


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2023-02-01 12:47:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/851#pullrequestreview-1279043601  

📁 include/boost/json/impl/array_view.ipp

```diff
  46 |+     virtual value_view      front(const void * data) const override
  47 |+     {
  48 |+         return value_view();
```

> Username: vinniefalco  
> Created_at: 2023-02-01 12:47:29 UTC  
> Updated_at: 2023-02-01 12:47:30 UTC  
> Url: https://github.com/boostorg/json/pull/851#discussion_r1093173219  

Shouldn't this throw?


---
