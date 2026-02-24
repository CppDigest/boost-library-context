# #160 Add value_builder [Closed]

> Username: vinniefalco  
> Created at: 2020-08-16 17:09:44 UTC  
> Updated at: 2020-08-16 20:56:55 UTC  
> Closed at: 2020-08-16 20:56:55 UTC  
> Url: https://github.com/boostorg/json/pull/160  

This makes the internal parser handler a public type.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-08-16 17:16:23 UTC  
> Url: https://github.com/boostorg/json/pull/160#issuecomment-674552757  

An automated preview of the documentation is available at [http://160.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://160.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 2 [Commented]

> Username: sdkrystian  
> Created_at: 2020-08-16 17:44:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/160#pullrequestreview-468081008  

I think we should write up a named requirement for handlers in the docs rather than documenting unused handlers in `value_builder`

📁 include/boost/json/value_builder.hpp

```diff
  71 |+     */
  72 |+     BOOST_JSON_DECL
  73 |+     virtual
```

> Username: sdkrystian  
> Created_at: 2020-08-16 17:26:22 UTC  
> Updated_at: 2020-08-16 17:48:04 UTC  
> Url: https://github.com/boostorg/json/pull/160#discussion_r471137381  

We moved away from the inheritance model, should this still be virtual?

> Username: vinniefalco  
> Created_at: 2020-08-16 17:46:57 UTC  
> Updated_at: 2020-08-16 17:48:04 UTC  
> Url: https://github.com/boostorg/json/pull/160#discussion_r471139416  

Nope

---

📁 include/boost/json/value_builder.hpp

```diff
 355 |+     */
 356 |+     bool
 357 |+     on_number_part(
```

> Username: sdkrystian  
> Created_at: 2020-08-16 17:28:11 UTC  
> Updated_at: 2020-08-16 17:48:04 UTC  
> Url: https://github.com/boostorg/json/pull/160#discussion_r471137563  

Instead of manually casting to `void`, we can just leave the parameters unnamed.

> Username: vinniefalco  
> Created_at: 2020-08-16 17:45:39 UTC  
> Updated_at: 2020-08-16 17:48:04 UTC  
> Url: https://github.com/boostorg/json/pull/160#discussion_r471139288  

Then the docs won't be right

---

📁 include/boost/json/value_builder.hpp

```diff
 397 |+         @param i The unsigned integer to build.
 398 |+ 
 399 |+         @param s The characters to append. This value is ignored.
```

> Username: sdkrystian  
> Created_at: 2020-08-16 17:28:41 UTC  
> Updated_at: 2020-08-16 17:48:04 UTC  
> Url: https://github.com/boostorg/json/pull/160#discussion_r471137606  

There is no parameter named `s` here.

---

📁 include/boost/json/value_builder.hpp

```diff
 427 |+     on_double(
 428 |+         double d,
 429 |+         string_view,
```

> Username: sdkrystian  
> Created_at: 2020-08-16 17:28:56 UTC  
> Updated_at: 2020-08-16 17:48:04 UTC  
> Url: https://github.com/boostorg/json/pull/160#discussion_r471137626  

There is no parameter named `s` here.

---

📁 include/boost/json/value_builder.hpp

```diff
 451 |+     /** Build a null.
 452 |+ 
 453 |+         This function builds a nullfrom the specified
```

> Username: sdkrystian  
> Created_at: 2020-08-16 17:29:31 UTC  
> Updated_at: 2020-08-16 17:48:04 UTC  
> Url: https://github.com/boostorg/json/pull/160#discussion_r471137688  

Missing space in "nullfrom", and there is no parameter name `ec` here.

---

📁 include/boost/json/value_builder.hpp

```diff
 474 |+     bool
 475 |+     on_comment_part(
 476 |+         string_view s,
```

> Username: sdkrystian  
> Created_at: 2020-08-16 17:31:11 UTC  
> Updated_at: 2020-08-16 17:48:04 UTC  
> Url: https://github.com/boostorg/json/pull/160#discussion_r471137870  

We can leave the parameters unnamed, as with all the other unused parameters.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-08-16 17:56:24 UTC  
> Url: https://github.com/boostorg/json/pull/160#issuecomment-674557016  

An automated preview of the documentation is available at [http://160.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://160.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---
