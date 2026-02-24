# #33 Add rvalue get<>() support + tests. [Merged]

> Username: ASverdlov  
> Created at: 2017-05-26 00:08:32 UTC  
> Updated at: 2017-05-30 20:38:16 UTC  
> Merged at: 2017-05-30 20:36:56 UTC  
> Closed at: 2017-05-30 20:36:56 UTC  
> Url: https://github.com/boostorg/variant/pull/33  

This PR adds rvalue version of get<>().  
Tests for expected behaviour and compile errors are added.  
Ticket: https://svn.boost.org/trac/boost/ticket/13018.

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2017-05-26 18:18:25 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/variant/pull/33#pullrequestreview-40585976  

Changes look good.  
  
But please fix the issues and double check the tests.  
  
Also check that the following compiles and does not introduce ambiguity:  
  
```  
const variant<int, std::string> foo1() { return ""; }  
variant<int, std::string> foo2() { return ""; }  
  
boost::get<std::string>(foo1());  
boost::get<std::string>(foo2());  
```

📁 include/boost/variant/get.hpp

```diff
  29 |+ #include <boost/type_traits/is_lvalue_reference.hpp>
  30 |+ 
  31 |+ #include <boost/mpl/not.hpp>
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:10:12 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118759818  

Use this header if you are doing some heavy metaprogramming and need lazy evaluation. Otherwise '!' is better.

---

📁 include/boost/variant/get.hpp

```diff
 274 |+ {
 275 |+     BOOST_STATIC_ASSERT_MSG(
 276 |+         (mpl::not_< boost::is_lvalue_reference<U> >::value),
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:11:50 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118760160  

Prefer `!boost::is_lvalue_reference<U>::value` (it is simpler and theoretically faster to compile because it instantiates less templates).


📁 test/Jamfile.v2

```diff
   7 | # Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at
   8 |- # http://www.boost.org/LICENSE_1_0.txt)
   8 |+ # http://www.boost.org/LICENSE_1_0.txt) #
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:12:28 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118760290  

Remove change in this line

---

📁 test/Jamfile.v2

```diff
  31 |     [ run variant_visit_test.cpp ]
  32 |     [ run variant_get_test.cpp ]
  33 |+     [ compile-fail variant_rvalue_get_with_ampersand_test.cpp ]
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:12:57 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118760398  

:+1:

---

📁 test/Jamfile.v2

```diff
  31 |     [ run variant_visit_test.cpp ]
  32 |     [ run variant_get_test.cpp ]
  33 |+     [ compile-fail variant_rvalue_get_with_ampersand_test.cpp ]
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:13:20 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118760469  

:+1:


📁 test/variant_get_test.cpp

```diff
 368 |+ private:
 369 |+     MoveonlyType(const MoveonlyType&);
 370 |+     MoveonlyType(MoveonlyType&&);
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:13:36 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118760533  

That's strange. Remove this line.

---

📁 test/variant_get_test.cpp

```diff
 378 |+   BOOST_CHECK(boost::get<MoveonlyType>(v));
 379 |+ 
 380 |+   boost::relaxed_get<MoveonlyType&>(boost::variant<int, MoveonlyType>()) // Must compile
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:15:02 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118760804  

Something is wrong here: this line must throw `bad_get()` because default constructed variant will hold an int.

---

📁 test/variant_get_test.cpp

```diff
 376 |+   v = MoveonlyType();
 377 |+   BOOST_CHECK(boost::get<MoveonlyType>(boost::move(v)));
 378 |+   BOOST_CHECK(boost::get<MoveonlyType>(v));
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:15:48 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118760943  

Something is wrong here.  
This two lines must not compile, because MoveonlyType is not convertible to bool.


📁 test/variant_rvalue_get_with_ampersand_test.cpp

```diff
   4 |+ //-----------------------------------------------------------------------------
   5 |+ //
   6 |+ // Copyright (c) 2014-2017 Antony Polukhin
```

> Username: apolukhin  
> Created_at: 2017-05-26 18:16:21 UTC  
> Updated_at: 2017-05-29 17:33:22 UTC  
> Url: https://github.com/boostorg/variant/pull/33#discussion_r118761052  

You are the author of this header - put your name into it :)


---

## Comment 2

> Username: apolukhin  
> Created_at: 2017-05-30 20:38:16 UTC  
> Url: https://github.com/boostorg/variant/pull/33#issuecomment-305000935  

Awesome! Thanks for the new feature!

---
