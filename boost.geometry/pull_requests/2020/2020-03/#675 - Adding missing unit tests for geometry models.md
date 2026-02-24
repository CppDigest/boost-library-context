# #675 Adding missing unit tests for geometry models [Merged]

> Username: digu-007  
> Created at: 2020-03-03 13:29:15 UTC  
> Updated at: 2020-03-10 08:32:51 UTC  
> Merged at: 2020-03-10 08:15:08 UTC  
> Closed at: 2020-03-10 08:15:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/675  

This PR is meant to fix #673. I have added unit tests for all the geometry models. Test includes working of default and copy constructors, assignment operator and custom test using `std::initializer_list`. I have covered tests for all the coordinate systems. I have tested all the files locally using GCC 7.4.

---

## Comment 1

> Username: digu-007  
> Created_at: 2020-03-04 12:05:14 UTC  
> Updated_at: 2020-03-04 12:24:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#issuecomment-594482585  

I added unit test for two more models `point` and `linestring`. @mloskot Can you review?  
  
Also, in [this](https://www.boost.org/doc/libs/1_72_0/libs/geometry/doc/html/geometry/reference/models/model_d2_point_xy.html) page same note is written twice, this is not deliberate right?

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-05 00:32:17 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/675#pullrequestreview-369220240  

Apart from nitpicks on the blanks, an issue I've got is with the `linestring` test.  
  
I'd prefer to see it first exercising [the constructors](https://www.boost.org/doc/libs/1_72_0/libs/geometry/doc/html/geometry/reference/models/model_linestring.html), and have the test cases targeting `linestring` with the default container and also with custom container (from `std` of course), and test cases split into functions.

📁 test/geometries/linestring.cpp

```diff
  33 |+ #include <boost/geometry/io/dsv/write.hpp>
  34 |+ 
  35 |+ #include <test_common/test_point.hpp>
```

> Username: mloskot  
> Created_at: 2020-03-05 00:24:33 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388013307  

That is too much of blank lines  :)

> Username: digu-007  
> Created_at: 2020-03-05 10:55:03 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388218921  

I thought it was a convention to leave a line if the header is from different directory, like in [segment.cpp](https://github.com/boostorg/geometry/blob/develop/test/geometries/segment.cpp), but I'll fix this :)

> Username: mloskot  
> Created_at: 2020-03-05 10:57:39 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388220253  

Not really, let's say it's unintentional legacy and any code should be crafted more carefully, compact, readable. See [#include directives shall be ordered...](https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers#code-structure) guidelines.


📁 test/geometries/point.cpp

```diff
  25 |+ 
  26 |+ 
  27 |+ template <typename T>
```

> Username: mloskot  
> Created_at: 2020-03-05 00:25:10 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388013477  

Please, remove the blanks


---

## Comment 3

> Username: digu-007  
> Created_at: 2020-03-05 12:49:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#issuecomment-595211876  

@mloskot I removed the extra lines and now I am using constructor for `linestring` and `initializer_list` for custom test. I have added more functions for better clarity of code.

---

## Review 4 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-06 00:16:00 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/675#pullrequestreview-370000873  

@digu-007 I suggested some ideas, please share what's your opinion.

📁 test/geometries/linestring.cpp

```diff
  58 |+ 
  59 |+ template <typename P>
  60 |+ void test_compilation()
```

> Username: mloskot  
> Created_at: 2020-03-06 00:12:06 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388637911  

Should read `test_concept`

---

📁 test/geometries/linestring.cpp

```diff
  51 |+ 
  52 |+ template <typename P>
  53 |+ void test_construction()
```

> Username: mloskot  
> Created_at: 2020-03-06 00:14:36 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388638657  

This in fact only tests default construction. Since we are at the unit test, I was wondering about such test cases  
  
- `test_default_constructor`  
- `test_copy_constructor`  
- `test_copy_assignment`

> Username: digu-007  
> Created_at: 2020-03-06 06:46:35 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388736708  

> what's your opinion  
  
Since it is unit tests, I will add `copy_constructor` and `copy_assignment` as well. I completely agree with you. Just one thing, adding so many assertions would make testing slow right? But not much I guess, what's the approximate complexity of these functions we use for testing in boost?

> Username: mloskot  
> Created_at: 2020-03-06 07:32:15 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388749565  

> adding so many assertions would make testing slow right?  
  
In our case the compilation time, due to code traits unrelated to use of assertions (e.g. meta-programming, header-only, etc.), is much much longer than run time of any of our tests, even if you stick hundreds of assertions.  
  
> But not much I guess, what's the approximate complexity of these functions we use for testing in boost?  
  
If you have `a == b`, complexity depends on type of the operands. If `a` and `b` are `int` it will be very different than if they are `std::vector<int>`, etc.  
  
Performance is not our concern here, but correctness (and coverage too).

> Username: digu-007  
> Created_at: 2020-03-06 08:36:42 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r388772268  

> Performance is not our concern here, but correctness.  
  
Yes, I agree. I'll add other functions as well in the next commit.


---

## Review 5 [Approved]

> Username: mloskot  
> Created_at: 2020-03-06 23:13:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/675#pullrequestreview-370681378  

LGTM. I have tested it locally with GCC 9.2

---

## Review 6 [Approved]

> Username: vissarion  
> Created_at: 2020-03-09 09:21:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/675#pullrequestreview-371009984  

Thanks! I am ok in general. I have a couple of comments.

📁 test/geometries/multi_linestring.cpp

```diff
   4 |+ // Copyright (c) 2020 Digvijay Janartha, Hamirpur, India.
   5 |+ 
   6 |+ // Parts of Boost.Geometry are redesigned from Geodan's Geographic Library
```

> Username: vissarion  
> Created_at: 2020-03-09 09:19:25 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r389539027  

Is this needed for completely new files?

> Username: digu-007  
> Created_at: 2020-03-09 09:41:57 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r389550663  

You mean line 6, shall I remove this in all the files?

> Username: mloskot  
> Created_at: 2020-03-09 09:42:14 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r389550802  

Good questions. I think the canonical form of the notice is just  
  
```  
// Boost.Geometry (aka GGL, Generic Geometry Library)  
//  
// Copyright (c) 2020 Digvijay Janartha, Hamirpur, India.  
//  
// Use, modification and distribution is subject to the Boost Software License,  
// Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt)  
```  
  
The other copyrights are added as necessary by authors of further modifications, that is according to [How should Boost programmers maintain the copyright messages?](https://www.boost.org/users/license.html).

> Username: digu-007  
> Created_at: 2020-03-09 09:50:52 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r389555277  

Ok I'll change to this form in the next commit, alongside tests for other cs as well :)

---

📁 test/geometries/multi_linestring.cpp

```diff
 137 |+ int test_main(int, char* [])
 138 |+ {   
 139 |+     test_all<bg::model::point<int, 2, bg::cs::cartesian> >();
```

> Username: vissarion  
> Created_at: 2020-03-09 09:20:35 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r389539578  

I think it make sense to also test geometry models with the other coordinate systems


---

## Review 7 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-09 19:30:45 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/675#pullrequestreview-371466049  

@digu-007 You need to ensure the tests build as C++03. For example, you can target all C++ versions with single `b2` command:  
  
```  
cd libs/geometry  
../../b2 cxxstd=03,11,14,17,2a test/geometries  
```  
  
As soon as you update your PR, you will get #680 change and GitHub Actions will CI check it for you building `b2 test//minimal` (not guaranteed all  of your code will be built though, but it will check PR does not break any and regress).

📁 test/geometries/linestring.cpp

```diff
   8 |+ // http://www.boost.org/LICENSE_1_0.txt)
   9 |+ 
  10 |+ #include <iostream>
```

> Username: mloskot  
> Created_at: 2020-03-09 19:26:47 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r389911125  

Here and in other `.cpp` files missing is  
  
```  
#ifdef BOOST_NO_CXX11_HDR_INITIALIZER_LIST  
#include <initializer_list>  
#endif  
```

---

📁 test/geometries/linestring.cpp

```diff
 105 |+     std::initializer_list<P> IL = {P(1, 2), P(2, 3), P(3, 4)};
 106 |+     test_custom_linestring<P>(IL);
 107 |+ }
```

> Username: mloskot  
> Created_at: 2020-03-09 19:27:35 UTC  
> Updated_at: 2020-03-10 05:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r389911521  

These test cases, and in the other `.cpp` files, require C++11 and must be wrapped with `BOOST_NO_CXX11_HDR_INITIALIZER_LIST`, see details at https://www.boost.org/libs/config/doc/html/boost_config/boost_macro_reference.html


---

## Review 8 [Commented]

> Username: mloskot  
> Created_at: 2020-03-10 08:12:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/675#pullrequestreview-371744539  

📁 test/geometries/linestring.cpp

```diff
  30 |+ #ifdef BOOST_NO_CXX11_HDR_INITIALIZER_LIST
  31 |+ #include <initializer_list>
  32 |+ #endif//BOOST_NO_CXX11_HDR_INITIALIZER_LIST
```

> Username: mloskot  
> Created_at: 2020-03-10 08:12:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r390147763  

I think you moved the include because you noticed `BOOST_NO_CXX11_HDR_INITIALIZER_LIST` is not defined.  
Yes, in order to get those macros defined, `#include <boost/config.hpp>` must be included first (and it is included by some of the `#include <boost/*>` headers.

> Username: digu-007  
> Created_at: 2020-03-10 08:30:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#discussion_r390155671  

> I think you moved the include because you noticed `BOOST_NO_CXX11_HDR_INITIALIZER_LIST` is not defined.  
  
Yes, I assumed so.


---

## Review 9 [Approved]

> Username: mloskot  
> Created_at: 2020-03-10 08:13:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/675#pullrequestreview-371745286  

This looks good and ready to merge. Thank you for your contribution.  
  
It has been checked by the GA jobs which build for C++03 through C++2a, so has been confirmed now your code does not require C++11 or later.

---

## Comment 10

> Username: digu-007  
> Created_at: 2020-03-10 08:32:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/675#issuecomment-596964138  

@mloskot @vissarion Thanks for all the suggestions and merging :)

---
