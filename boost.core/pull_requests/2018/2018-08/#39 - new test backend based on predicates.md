# #39 new test backend based on predicates [Merged]

> Username: HDembinski  
> Created at: 2018-08-06 09:49:09 UTC  
> Updated at: 2018-08-08 12:58:34 UTC  
> Merged at: 2018-08-08 12:37:51 UTC  
> Closed at: 2018-08-08 12:37:51 UTC  
> Url: https://github.com/boostorg/core/pull/39  

See #38   
  
The test backend uses predicates now, which reduces code duplication and makes the system more flexible. I implemented BOOST_TEST_CLOSE using this new flexibility (also added tests).  
  
BOOST_TEST_CLOSE uses the predicate `close_to` suggested by @breese.

---

## Review 1 [Changes requested]

> Username: glenfe  
> Created_at: 2018-08-06 11:16:49 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/core/pull/39#pullrequestreview-143549247  

Please move `close_to` to the `boost::core::detail` namespace. It can live within lightweight_test.hpp instead of a separate header file.

---

## Review 2 [Changes requested]

> Username: glenfe  
> Created_at: 2018-08-06 11:27:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/core/pull/39#pullrequestreview-143550710  

📁 include/boost/core/close_to.hpp

```diff
  35 |+     {}
  36 |+ 
  37 |+     bool operator() (const T& lhs, const T& rhs)
```

> Username: glenfe  
> Created_at: 2018-08-06 11:22:13 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207857090  

`const`

---

📁 include/boost/core/close_to.hpp

```diff
  29 |+ struct close_to
  30 |+ {
  31 |+     close_to(T relative_tolerance = std::numeric_limits<T>::epsilon(),
```

> Username: glenfe  
> Created_at: 2018-08-06 11:22:35 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207857189  

`explicit`


📁 include/boost/core/lightweight_test.hpp

```diff
 153 |- }
 126 |+ template <typename T>
 127 |+ struct test_output_traits<std::equal_to<T> > {
```

> Username: glenfe  
> Created_at: 2018-08-06 11:24:54 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207857743  

There are Boost libraries using Boost.Test even for facilities that need to support implementations where `BOOST_NO_TEMPLATE_PARTIAL_SPECIALIZATION` is defined.

> Username: HDembinski  
> Created_at: 2018-08-06 13:35:32 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207892987  

Ok, I could change these to functions overloads and thus avoid partial specialization.

---

📁 include/boost/core/lightweight_test.hpp

```diff
 179 |         BOOST_LIGHTWEIGHT_TEST_OSTREAM
 199 |-             << file << "(" << line << "): test '" << expr1 << " > " << expr2
 180 |+             << file << "(" << line << "): test '" << expr1 << " " << test_output_traits<BinaryPredicate>::op() << " " << expr2
```

> Username: glenfe  
> Created_at: 2018-08-06 11:26:26 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207858217  

Instead of `test_output_traits` the helper utility used here could also receive an output string.

> Username: HDembinski  
> Created_at: 2018-08-06 13:38:30 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207893878  

I am not sure I understand.


---

## Comment 3

> Username: pdimov  
> Created_at: 2018-08-06 11:41:40 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-410680385  

It seems to me that converting the second argument to the type of the first would break `BOOST_TEST_GT('x', -1)`, `BOOST_TEST_EQ(nullptr, p)`, `BOOST_TEST_NE(&y, &x)` where `Y` derives from `X`, and so on.

---

## Review 4 [Changes requested]

> Username: glenfe  
> Created_at: 2018-08-06 11:48:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/core/pull/39#pullrequestreview-143557495  

📁 include/boost/core/lightweight_test.hpp

```diff
 219 |-         ++test_errors();
 220 |-     }
 192 |+     test_with_impl(expr1, expr2, file, line, function, t, u, BinaryPredicate<T>());
```

> Username: glenfe  
> Created_at: 2018-08-06 11:47:29 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207863131  

This is wrong when you pass in `std::equal_to` as the predicate. Before it would check `t == u` which could call `operator==(const T&, const U&)`. Now with your change it can't call such an operator, and would instead convert `U` to `T`.  You don't want `BinaryPredicate<T>` here, because you don't want `std::equal_to<T>`. You want `std:::equal_to<void>` but that isn't available until more recent C++ standards.

---

📁 include/boost/core/lightweight_test.hpp

```diff
 206 |- template<class T, class U> inline void test_ge_impl( char const * expr1, char const * expr2,
 207 |-   char const * file, int line, char const * function, T const & t, U const & u )
 187 |+ template<template <class> class BinaryPredicate, class T, class U>
```

> Username: glenfe  
> Created_at: 2018-08-06 11:47:47 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207863190  

There's also no need to confine the predicates to only templates of the form `template<class>`


---

## Comment 5

> Username: breese  
> Created_at: 2018-08-06 12:18:52 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-410688697  

@glenfe `close_to` is not really restricted to unit-tests. It is useful wherever you have to compare floats for equality, e.g.  
```  
  if (close_to()(number, 0.4)) { /* almost 0.4 */ }  
```  
It would even make sense to propose it for inclusion in `<functional>`.

---

## Review 6 [Commented]

> Username: breese  
> Created_at: 2018-08-06 12:20:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/39#pullrequestreview-143567172  

📁 include/boost/core/close_to.hpp

```diff
  42 |+ private:
  43 |+     T relative;
  44 |+     T absolute;
```

> Username: breese  
> Created_at: 2018-08-06 12:20:22 UTC  
> Updated_at: 2018-08-08 07:52:30 UTC  
> Url: https://github.com/boostorg/core/pull/39#discussion_r207871300  

Member variables also be made `const`


---

## Comment 7

> Username: glenfe  
> Created_at: 2018-08-06 14:19:16 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-410724116  

@breese When or if a Boost library has a use for it, it can be moved out of the detail namespace, and then properly documented (and tested independently of the Lightweight test macros) as part of Boost.Core.

---

## Comment 8

> Username: HDembinski  
> Created_at: 2018-08-06 18:58:12 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-410816266  

I think the new version addresses all the concerns, let me know if I forgot something.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-08-06 20:38:11 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-410845405  

This looks sensible and is very close to what I envisaged as a design. Would you please rename the predicates along the lines of `lw_test_eq` instead of the generic `equal_to` so as not to pollute `detail`, and perhaps split this PR into two, one refactoring the existing functionality into predicate form, a separate one introducing `close_to`?

---

## Comment 10

> Username: HDembinski  
> Created_at: 2018-08-07 08:49:43 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-410983223  

Ok, will do. I was am aware of the "one-topic per PR" rule, but then considered the two patches to be so closely related, that I put them in one. Also to help with the discussion, so that it is clear where this is going.

---

## Comment 11

> Username: HDembinski  
> Created_at: 2018-08-07 09:06:02 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-410988054  

I suppose you also want a separate PR for line 209, where I corrected a mistake in the output print.

---

## Comment 12

> Username: glenfe  
> Created_at: 2018-08-07 09:23:34 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-410993130  

> I suppose you also want a separate PR for line 209, where I corrected a mistake in the output print.  
  
No, you can include that in the current PR. :)

---

## Comment 13

> Username: HDembinski  
> Created_at: 2018-08-07 09:50:51 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-411000975  

Ok, that's a relief :)

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-08-07 12:16:25 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-411035670  

As a matter of fact yes I do. -)  
  
The reason to split separate things into their own PRs is not to adhere to a rule, but to allow them to be merged separately without being tied up together (and then reverted separately if need be.) This way straightforward changes can be applied immediately without waiting for the rest to crystallize.  
  
Could you move the BinaryPredicate to be the first argument of `test_with_impl`? Thanks.

---

## Comment 15

> Username: HDembinski  
> Created_at: 2018-08-07 12:38:10 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-411041323  

@pdimov the predicate is also the last argument in `test_all_with_impl`. If I do as you suggest, it is inconsistent between `test_with_impl` and `test_all_with_impl`

---

## Comment 16

> Username: HDembinski  
> Created_at: 2018-08-07 12:41:11 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-411042134  

Otherwise I think I see why you request this. It is simply easier to see whether the correct predicate is used for a given BOOST_TEST... call when it is the first argument. But then we perhaps should also change `test_all_with_impl` in the same way.

---

## Comment 17

> Username: pdimov  
> Created_at: 2018-08-07 13:12:32 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-411050819  

Yes, I understand why you followed the style of the existing `test_all_with_impl`, but I still prefer the predicate first in this case. Let's leave `test_all_with_impl` as is.

---

## Comment 18

> Username: pdimov  
> Created_at: 2018-08-07 22:30:02 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-411223786  

Instead of `BinaryPredicate::op()`, `pred.op()` please. Makes no difference here, of course, but in principle, the `op` may depend on state. (The `anti_op` for `close_to(0.001)` could for instance return `!=[within 0.001]`.)  
  
Could also rebase on develop to pick up the new tests I added.

---

## Comment 19

> Username: HDembinski  
> Created_at: 2018-08-08 07:47:27 UTC  
> Updated_at: 2018-08-08 07:58:43 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-411318138  

That is a great idea, I would like that for `close_to`. Also I see now that I unnecessarily restricted the code, blocking possible future extensions.

---

## Comment 20

> Username: HDembinski  
> Created_at: 2018-08-08 12:58:34 UTC  
> Url: https://github.com/boostorg/core/pull/39#issuecomment-411395987  

Thank you everyone for the merge and review!

---
