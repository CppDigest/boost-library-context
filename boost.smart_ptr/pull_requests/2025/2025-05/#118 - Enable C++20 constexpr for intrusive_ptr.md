# #118 Enable C++20 constexpr for intrusive_ptr [Merged]

> Username: RedBeard0531  
> Created at: 2025-05-08 12:13:57 UTC  
> Updated at: 2025-05-10 08:52:28 UTC  
> Merged at: 2025-05-10 08:52:28 UTC  
> Closed at: 2025-05-10 08:52:28 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118  

I opted for the maximalist approach of making anything that could reasonably be constexpr be so. That means everything other than working with ostream is now. I also added inline to the free-functions that were not already marked as such but were being made constexpr because constexpr implies inline on functions. It felt wrong for a function's inlineness to depend on which C++ standard you compile with. It mostly doesn't matter because the functions were already templates.  
  
Fixes #117

---

## Review 1 [Commented]

> Username: RedBeard0531  
> Created_at: 2025-05-08 12:18:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/smart_ptr/pull/118#pullrequestreview-2824944371  

📁 test/sp_constexpr_test2.cpp

```diff
  71 |+   // no-ops, so safe on pointers to static constexpr variables
  72 |+   friend constexpr void intrusive_ptr_add_ref(const dummy *) {}
  73 |+   friend constexpr void intrusive_ptr_release(const dummy *) {}
```

> Username: RedBeard0531  
> Created_at: 2025-05-08 12:18:59 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2079606624  

For my use case I only care about being able to create null intrusive_ptr objects in constant expressions. But I could  imagine someone wanting it to work with a non-atomic ref count as well. This seems sufficient to test that intrusive_ptr would support that use case without actually building that thing.


---

## Comment 2

> Username: pdimov  
> Created_at: 2025-05-08 12:56:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#issuecomment-2862956929  

1. The macro should be BOOST_SP_CXX20_CONSTEXPR and the header name should be sp_cxx20_constexpr;  
2. Please don't add the inlines;  
3. sp_constexpr_test2.cpp tests something else (constant initialization) and the tests for constexpr intrusive_ptr don't belong there. A new test needs to be added, ip_constexpr_test.cpp, say.  
4. Ideally, every added constexpr should be covered by a test.  
5. I need to fix the CI.

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2025-05-09 09:05:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/smart_ptr/pull/118#pullrequestreview-2827614509  

📁 test/sp_constexpr_test2.cpp

```diff
  67 | 
  68 |- #endif
  68 |+ #endif
```

> Username: pdimov  
> Created_at: 2025-05-09 09:05:24 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081262567  

This change shouldn't be there.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2025-05-09 09:05:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/smart_ptr/pull/118#pullrequestreview-2827615681  

📁 test/ip_constexpr_test.cpp

```diff
  74 |+ static_assert(test_reset_assign());
  75 |+ 
  76 |+ #endif
```

> Username: pdimov  
> Created_at: 2025-05-09 09:05:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081263120  

The lack of a newline at the end is probably not intentional.

> Username: RedBeard0531  
> Created_at: 2025-05-09 09:20:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081287072  

Sorry, just realized that my config to add it is workspace specific. Will fix.  
  
PS - it'd be great to add a .clang-format to the repo root so that formatting things are handled automatically. I've had to run "save without formatting" to avoid reflowing the whole file every time I save.


---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2025-05-09 09:08:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/smart_ptr/pull/118#pullrequestreview-2827623264  

📁 test/ip_constexpr_test.cpp

```diff
  23 |+ 
  24 |+ // Test that basic operations work at compile time.
  25 |+ static_assert(bool(boost::intrusive_ptr(&d)));
```

> Username: pdimov  
> Created_at: 2025-05-09 09:08:33 UTC  
> Updated_at: 2025-05-09 09:08:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081268054  

We don't want to test CTAD here; please use `intrisuve_ptr<dummy>` everywhere for consistency.

> Username: RedBeard0531  
> Created_at: 2025-05-09 09:17:06 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081281255  

It has to be `intrusive_ptr<const dummy>` which gets noisy fast. Do you still want that? I guess I could define a typedef.

> Username: pdimov  
> Created_at: 2025-05-09 09:25:32 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081295141  

Yes, I do want it, without the typedef.


---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2025-05-09 09:12:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/smart_ptr/pull/118#pullrequestreview-2827633649  

📁 test/ip_constexpr_test.cpp

```diff
  27 |+ static_assert(!bool(boost::intrusive_ptr<dummy>()));
  28 |+ static_assert(!bool(boost::intrusive_ptr<dummy>(boost::intrusive_ptr<subdummy>())));
  29 |+ static_assert(&*boost::intrusive_ptr(&d));
```

> Username: pdimov  
> Created_at: 2025-05-09 09:12:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081274691  

The results of the operations should be tested too; `&*boost::intrusive_ptr<dummy>(&d) == &d`

> Username: RedBeard0531  
> Created_at: 2025-05-09 09:15:51 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081279318  

I've been assuming that this test is primarily to test that the compiler accepts these operations at compile time, and that correctness tests are handled by the existing tests. Is that not acceptable?

> Username: pdimov  
> Created_at: 2025-05-09 09:19:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081285887  

Since we're testing the result in either case (against nullptr), we might as well test it for the right value.  
  
At the moment this makes no difference but only because we know that the implementation doesn't do `if consteval` anywhere. Tests should not assume knowledge of the implementation.


---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2025-05-09 09:30:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/smart_ptr/pull/118#pullrequestreview-2827679863  

📁 include/boost/smart_ptr/detail/sp_cxx20_constexpr.hpp

```diff
  27 |+ // constant expressions which is allowed in C++20. If that is needed, we
  28 |+ // need to raise the checked version to 202002L.
  29 |+ #if defined(__cpp_constexpr_dynamic_alloc) and (__cpp_constexpr >= 201907L)
```

> Username: pdimov  
> Created_at: 2025-05-09 09:30:48 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081303195  

Should be  
  
`defined(__cpp_constexpr_dynamic_alloc) && __cpp_constexpr_dynamic_alloc >= 201907L && defined(__cpp_constexpr) && __cpp_constexpr >= 201907L`  
  
msvc doesn't like `and` and neither do I :-)

> Username: RedBeard0531  
> Created_at: 2025-05-09 09:33:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081306726  

facepalm. I normally don't either. I've been writing too much python and jq lately...

> Username: RedBeard0531  
> Created_at: 2025-05-09 09:47:02 UTC  
> Updated_at: 2025-05-09 09:47:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081335814  

Are there any compilers that don't implement https://eel.is/c++draft/cpp.cond#12 correctly enough for `(__cpp_constexpr >= 201907L)` to be equivalent to `defined(__cpp_constexpr) && __cpp_constexpr >= 201907L` in the context of an `#if`?  
  
`201907L` is the lowest defined value of `__cpp_constexpr_dynamic_alloc` so it shouldn't be necessary to check it.

> Username: pdimov  
> Created_at: 2025-05-09 09:53:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081345532  

I'm not sure whether a compiler is allowed to define a feature macro to 0, 1, or some other value lower than 201907L, and I prefer not to have to know. Let's just use the idiomatic check everywhere.

> Username: pdimov  
> Created_at: 2025-05-09 09:53:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#discussion_r2081346711  

> Are there any compilers that don't implement https://eel.is/c++draft/cpp.cond#12 correctly enough  
  
Yes, if you use -Wundef -Werror.


---

## Comment 8

> Username: pdimov  
> Created_at: 2025-05-09 16:39:28 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#issuecomment-2867206535  

Looks good. Do you want to massage the commit history so that it's clean, or do you want me to just squash-merge?

---

## Comment 9

> Username: RedBeard0531  
> Created_at: 2025-05-09 20:38:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/118#issuecomment-2867827583  

Cleaned and ready to merge.

---
