# #59 recursive_wrapper move constructor pointer stealing [Merged]

> Username: Kojoley  
> Created at: 2019-01-31 18:38:04 UTC  
> Updated at: 2019-04-23 11:40:25 UTC  
> Merged at: 2019-04-12 07:52:36 UTC  
> Closed at: 2019-04-12 07:52:36 UTC  
> Url: https://github.com/boostorg/variant/pull/59  

Instead of allocating a new object just steal the pointer from the other  
recursive_wrapper. It is much cheaper and allows to mark the move constructor  
noexcept (allows variant to move the object without backup copying it).  
  
Fixes #58.

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-01-31 18:54:29 UTC  
> Updated_at: 2019-03-04 04:27:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#issuecomment-459462296  

[![Coverage Status](https://coveralls.io/builds/21962073/badge)](https://coveralls.io/builds/21962073)  
  
Coverage increased (+0.2%) to 95.278% when pulling **5c1039909665fa768e081246590d57932af676f9 on Kojoley:recursive_wrapper-move-constructor-pointer-stealing** into **d069511e31a3b7fee45e22634bb43b8f4e4608cd on boostorg:develop**.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-02-01 01:34:46 UTC  
> Updated_at: 2019-02-01 01:35:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#issuecomment-459573177  

Well, Joel pointed me to a thread from 2013 on this subject http://boost.2283326.n4.nabble.com/variant-Basic-rvalue-and-C-11-features-support-tt4637894.html#none. After reading it I found two concerns about making the same changes as in this PR:  
  1. An object after moving out should be in a valid state.  
  2. A moved out object that continues to live somewhere must still be usable (was mentioned `operator<`).  
  
All they are satisfied:  
  1. The nullified `recursive_wrapper` is in a valid but unspecified state like an standard library types. [\[lib.types.movedfrom\]](http://eel.is/c++draft/lib.types.movedfrom)  
     Actually it is even in a know state - `get_pointer() == nullptr`, but it is an implementation detail.  
  2. After moving out from `recursive_wrapper` some operations on it are not valid because they have a precondition like for standard library types. [\[defns.valid\]](http://eel.is/c++draft/defns.valid)  
  
     Examples:  
  
     These operations can be called unconditionally:  
       - `get_pointer()`  
       - `operator=(recursive_wrapper&&)`  
  
     But these operations require `get_pointer() != nullptr`:  
       - `operator<`  
       - `operator=(T&&)`  
  
     So they cannot be called without checking `get_pointer() != nullptr` or before turning the object into a specified state (copy assign an object in specified state).

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-02-01 14:32:43 UTC  
> Updated_at: 2019-02-24 15:14:53 UTC  
> Url: https://github.com/boostorg/variant/pull/59#issuecomment-459741368  

After scratching my head I see the main problems of the PR:  
  - Users may misinterpret ["Never-Empty" Guarantee](https://www.boost.org/doc/libs/1_69_0/doc/html/variant/design.html#variant.design.never-empty) that was designed for throwing copy, and existed before move semantics. This is a documentation issue and can be easily be fixed.  
  - <strike>Currently there is no way to lift this `recursive_wrapper` dereferencing precondition to `variant` visitation. It can be fixed by providing a method `valueless_by_move` that will guaranteed to always return false for variants without `recursive_wrapper` or if `which()` does not point to an active `recursive_wrapper`ed type.</strike> The `variant` has `empty()` method which currently always returns `false`, after the change it will return `true` on recursive moved-out variants.

---

## Review 4 [Changes requested]

> Username: apolukhin  
> Created_at: 2019-02-04 08:56:34 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/variant/pull/59#pullrequestreview-199480695  

📁 include/boost/variant/recursive_wrapper.hpp

```diff
 129 |-     : p_(new T( detail::variant::move(operand.get()) ))
 129 |+ recursive_wrapper<T>::recursive_wrapper(recursive_wrapper&& operand) BOOST_NOEXCEPT
 130 |+     : p_(boost::exchange(operand.p_, nullptr))
```

> Username: apolukhin  
> Created_at: 2019-02-04 08:54:08 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r253382940  

`nullptr` may not be available. Use 0 :(

> Username: Kojoley  
> Created_at: 2019-02-08 17:17:11 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r255161668  

Fixed

---

📁 include/boost/variant/recursive_wrapper.hpp

```diff
  17 | #include <boost/variant/detail/move.hpp>
  18 | #include <boost/checked_delete.hpp>
  19 |+ #include <boost/core/exchange.hpp>
```

> Username: apolukhin  
> Created_at: 2019-02-04 08:55:18 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r253383258  

Add `BOOST_ASSERT` for all the cases when recursive_wrapper is dereferenced.

> Username: apolukhin  
> Created_at: 2019-02-04 08:57:15 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r253383922  

Mostly for all the `apply_visitor`s

> Username: Kojoley  
> Created_at: 2019-02-08 17:17:55 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r255161894  

Fixed by adding assertation to `recursive_wrapper::get` method which is used in every apply_visitor.

---

📁 include/boost/variant/recursive_wrapper.hpp

```diff
 128 | template <typename T>
 128 |- recursive_wrapper<T>::recursive_wrapper(recursive_wrapper&& operand)
 129 |-     : p_(new T( detail::variant::move(operand.get()) ))
```

> Username: apolukhin  
> Created_at: 2019-02-04 08:56:28 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r253383610  

Could we have a macro to restore the old behavior? Just for some time, so that users could migrate simpler.

> Username: Kojoley  
> Created_at: 2019-02-08 17:18:23 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r255162072  

I have added macro, though I am not sure about its name.


---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-02-15 21:14:53 UTC  
> Url: https://github.com/boostorg/variant/pull/59#issuecomment-464201924  

It seems to me that `recursive_wrapper` assignment operators need to be changed so they could be used to reuse an empty `recursive_wrapper`.  
Also I can try make `variant` relation operators to work with empty variants too.

---

## Review 6 [Changes requested]

> Username: apolukhin  
> Created_at: 2019-02-21 07:58:39 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/variant/pull/59#pullrequestreview-206158786  

Looks almost perfect. Great thanks for doing this!  
  
Do not fix the comparison operators for now

📁 include/boost/variant/recursive_wrapper.hpp

```diff
  46 | 
  47 | #ifndef BOOST_NO_CXX11_RVALUE_REFERENCES 
  48 |+ #ifndef BOOST_VARIANT_NO_RECURSIVE_WRAPPER_POINTER_STEALING
```

> Username: apolukhin  
> Created_at: 2019-02-21 07:43:49 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r258809358  

Please add docs for that macro and also note it near the `empty()` functions.

> Username: Kojoley  
> Created_at: 2019-02-23 21:23:46 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r259593715  

Should I update never-empty guarantee page with a note about recursive variants can be empty?

> Username: apolukhin  
> Created_at: 2019-03-02 08:38:43 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r261819411  

Yes please

> Username: Kojoley  
> Created_at: 2019-03-03 22:50:00 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r261892280  

Done.


📁 include/boost/variant/variant.hpp

```diff
2237 |     {
2203 |-         return false;
2238 |+         detail::variant::empty_check_visitor visitor;
```

> Username: apolukhin  
> Created_at: 2019-02-21 07:46:34 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r258809979  

Use the `BOOST_VARIANT_NO_RECURSIVE_WRAPPER_POINTER_STEALING` macro here for the users that wish to have the old behavior.

> Username: Kojoley  
> Created_at: 2019-02-23 21:22:31 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r259593689  

Done


📁 test/recursive_wrapper_move_only_test.cpp

```diff
  65 |+     BOOST_TEST( b.empty());
  66 |+     BOOST_TEST_EQ(boost::get<move_only_type>(c).value_, 2);
  67 |+     return boost::report_errors();
```

> Username: apolukhin  
> Created_at: 2019-02-21 07:57:15 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r258812462  

Add tests for throwing move assignments and recursive wrapper. Make sure that if the type `A` has a throwing default constructor and an always throwing move assignment then the following code works:  
```  
variant<A, recursive_wrapper<int>> a(A()), rec(1);  
try {  
    rec = std::move(a);  
    Assert(false);  
} catch (...) { BOOST_TEST(!rec.empty()); }  
```  
  
Also make sure that   
```  
variant<A, recursive_wrapper<int>> a(A()), rec(1), rec2(2);  
rec2 = std::move(rec)  
try {  
    rec = std::move(a);  
    Assert(false);  
} catch (...) { BOOST_TEST(rec.empty()); }  
rec = std::move(rec2)  
```  
is fine.

> Username: Kojoley  
> Created_at: 2019-02-23 21:22:06 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r259593675  

From what I see variant will never use throwing move even if copy can throw too (variant also does not use move assignment, as it constructs a temporary variant and swaps the content). In the second example I am do not understand why `rec` expected to be empty after failed move.

> Username: apolukhin  
> Created_at: 2019-03-02 08:46:16 UTC  
> Updated_at: 2019-03-03 22:49:40 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r261819627  

You are right, `rec` could be anything. Just make sure that it destructs well after the `throw` in move assignment.

> Username: Kojoley  
> Created_at: 2019-03-03 18:42:35 UTC  
> Updated_at: 2019-03-03 22:51:13 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r261880647  

I am not sure how can I check if it destructs "well". Also, is not the first example does it already?

> Username: Kojoley  
> Created_at: 2019-03-14 21:46:43 UTC  
> Updated_at: 2019-03-14 21:46:44 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r265779149  

Ping

> Username: apolukhin  
> Created_at: 2019-03-15 16:08:04 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r266048221  

Everything looks great. I'll merge the PR after the 1.70 release (I'm not comfortable with merging it now, without a few weeks of usage in develop)

> Username: Kojoley  
> Created_at: 2019-03-15 16:16:24 UTC  
> Url: https://github.com/boostorg/variant/pull/59#discussion_r266051258  

Ok, thanks for the clarification.


---
