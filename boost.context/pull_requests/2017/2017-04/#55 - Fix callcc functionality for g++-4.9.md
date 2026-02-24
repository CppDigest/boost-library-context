# #55 Fix callcc functionality for g++-4.9 [Merged]

> Username: DaoWen  
> Created at: 2017-04-20 23:47:00 UTC  
> Updated at: 2017-04-22 14:29:23 UTC  
> Merged at: 2017-04-22 14:29:23 UTC  
> Closed at: 2017-04-22 14:29:23 UTC  
> Url: https://github.com/boostorg/context/pull/55  

Fixes the resolution issue discussed in #52 and #54 that caused compiler errors when using g++-4.9. Also fixes a bad cast that was causing bad values to be passed as arguments into continuations.

---

## Review 1 [Commented]

> Username: DaoWen  
> Created_at: 2017-04-20 23:52:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/context/pull/55#pullrequestreview-33911471  

I tested this patch on x86_64 Linux with g++-4.9, g++-5.4, and g++-6.2. All tests passed for all tested compiler toolchains.

📁 include/boost/context/continuation.hpp

```diff
 454 |-     typename = detail::disable_overload< continuation, Fn >
 455 |+     typename = detail::disable_overload< continuation, Fn >,
 456 |+     typename = detail::disable_overload< std::allocator_arg_t, Fn >
```

> Username: DaoWen  
> Created_at: 2017-04-20 23:47:28 UTC  
> Updated_at: 2017-04-20 23:52:50 UTC  
> Url: https://github.com/boostorg/context/pull/55#discussion_r112584017  

This extra `disable_overload` fixes the compiler error for me.

---

📁 include/boost/context/continuation.hpp

```diff
 356 |     continuation resume_with( Fn && fn, Arg ... arg) {
 357 |         BOOST_ASSERT( nullptr != t_.fctx);
 357 |-         auto tpl = std::make_tuple( std::forward< Fn >( fn), std::forward< Arg >( arg) ... );
```

> Username: DaoWen  
> Created_at: 2017-04-20 23:48:19 UTC  
> Updated_at: 2017-04-20 23:59:30 UTC  
> Url: https://github.com/boostorg/context/pull/55#discussion_r112584096  

Note that the type of `tpl` here is `std::tuple<Fn, Arg...>`.

---

📁 include/boost/context/continuation.hpp

```diff
 242 |+ template< typename Ctx, typename Fn, typename Arg >
 243 | detail::transfer_t context_ontop( detail::transfer_t t) {
 244 |-     auto p = static_cast< std::tuple< Fn, std::tuple< Arg ... > > * >( t.data);
```

> Username: DaoWen  
> Created_at: 2017-04-20 23:49:20 UTC  
> Updated_at: 2017-04-20 23:52:51 UTC  
> Url: https://github.com/boostorg/context/pull/55#discussion_r112584206  

Note that the expected type for `t.data` is `std::tuple< Fn, std::tuple< Arg ... > > *`, which does not correspond to the argument type declared on line 357 below.  
  
My guess is that the C++ library implementation for `std::tuple` changed between g++-4.9 and g++-5, which is why this bad cast happened to work with newer g++ versions but fails with version 4.9.


---

## Comment 2

> Username: olk  
> Created_at: 2017-04-22 14:29:16 UTC  
> Url: https://github.com/boostorg/context/pull/55#issuecomment-296376940  

hmm, did noticed that mistake - strage that I got fooled by so much compilers (clang, msvc, intel compile this code).  
ty for fixing it.

---
