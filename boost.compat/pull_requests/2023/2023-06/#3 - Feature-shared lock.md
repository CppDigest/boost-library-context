# #3 Feature/shared lock [Merged]

> Username: cmazakas  
> Created at: 2023-06-23 17:57:48 UTC  
> Updated at: 2023-07-03 15:29:30 UTC  
> Merged at: 2023-06-25 08:47:01 UTC  
> Closed at: 2023-06-25 08:47:01 UTC  
> Url: https://github.com/boostorg/compat/pull/3  

* add a clang-format file that emulates author styles  
* implement first draft support of `shared_lock` for C++11 sans TimedLockable support  
* add relevant docs

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2023-06-23 22:51:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/3#pullrequestreview-1496157494  

📁 doc/compat/shared_lock.adoc

```diff
  78 |+ } // namespace compat
  79 |+ 
  80 |+ using boost::compat::swap;
```

> Username: pdimov  
> Created_at: 2023-06-23 22:51:11 UTC  
> Updated_at: 2023-06-23 22:51:12 UTC  
> Url: https://github.com/boostorg/compat/pull/3#discussion_r1240482262  

This shouldn't be here.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2023-06-23 22:53:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/3#pullrequestreview-1496158959  

📁 include/boost/compat/shared_lock.hpp

```diff
  25 |+ 
  26 |+ BOOST_NORETURN BOOST_NOINLINE inline void
  27 |+ throw_system_error( std::errc e, boost::source_location const& loc ) {
```

> Username: pdimov  
> Created_at: 2023-06-23 22:53:48 UTC  
> Url: https://github.com/boostorg/compat/pull/3#discussion_r1240483345  

You can add a default argument, `loc = BOOST_CURRENT_LOCATION`, to avoid having to pass it each time.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2023-06-23 22:56:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/3#pullrequestreview-1496162255  

📁 test/Jamfile

```diff
  27 | run latch_test.cpp ;
  28 |+ run shared_lock_test.cpp ;
  29 |+ run shared_lock_test.cpp : : : <exception-handling>off <toolset>msvc:<define>_HAS_EXCEPTIONS=0 : no_exceptions_shared_lock_test ;
```

> Username: pdimov  
> Created_at: 2023-06-23 22:56:11 UTC  
> Url: https://github.com/boostorg/compat/pull/3#discussion_r1240485400  

`<toolset>msvc:<define>_HAS_EXCEPTIONS=0` is wrong and shouldn't be here.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2023-06-23 22:57:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/3#pullrequestreview-1496162897  

📁 test/shared_lock_test.cpp

```diff
  22 |+ BOOST_NORETURN void throw_exception( std::exception const&,
  23 |+                                      boost::source_location const& ) {
  24 |+   std::terminate();
```

> Username: pdimov  
> Created_at: 2023-06-23 22:57:20 UTC  
> Url: https://github.com/boostorg/compat/pull/3#discussion_r1240485824  

fprintf something to stderr here.


---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2023-06-23 23:00:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/3#pullrequestreview-1496163236  

📁 test/shared_lock_test.cpp

```diff
  54 |+     if ( lock_shared_count_ != unlock_shared_count_ ) {
  55 |+       boost::compat::detail::throw_system_error(
  56 |+           std::errc::resource_deadlock_would_occur, BOOST_CURRENT_LOCATION );
```

> Username: pdimov  
> Created_at: 2023-06-23 22:59:00 UTC  
> Updated_at: 2023-06-23 23:00:25 UTC  
> Url: https://github.com/boostorg/compat/pull/3#discussion_r1240486267  

I don't think you should be using internal functions here. Throw something else.


---
