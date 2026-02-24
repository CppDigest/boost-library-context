# #394 Add std::is_constant_evaluated support. [Open]

> Username: jzmaddock  
> Created at: 2021-08-08 18:26:38 UTC  
> Updated at: 2022-08-14 14:43:58 UTC  
> Url: https://github.com/boostorg/config/pull/394  



---

## Review 1 [Commented]

> Username: libbooze  
> Created_at: 2021-08-09 23:12:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/394#pullrequestreview-725864332  

📁 doc/macro_reference.qbk

```diff
1103 |+                                  of program optimisation.
1104 |+ 
1105 |+                                  The macro always evaluates to `false` on unsupported/old compilers.]]
```

> Username: libbooze  
> Created_at: 2021-08-09 22:33:59 UTC  
> Updated_at: 2021-08-09 23:12:08 UTC  
> Url: https://github.com/boostorg/config/pull/394#discussion_r685564933  

From what I see Implementation in some cases uses ` std::is_constant_evaluated` , in some `__builtin_constant_p`. I found no guarantees in documentation that `__builtin_constant_p  `must return true in constexpr evaluation. Intuitively during constexpr function evaluation it should always return true, but I am not sure that is guaranteed. Consider this [tricky case]( https://www.godbolt.org/z/bqrqTonoG) .

> Username: jzmaddock  
> Created_at: 2021-08-10 17:04:11 UTC  
> Url: https://github.com/boostorg/config/pull/394#discussion_r686160373  

Nod.  There will certainly be tricky cases where you can abuse it into doing the wrong thing, if it's easier I could just omit that macro.

> Username: libbooze  
> Created_at: 2021-08-10 18:09:37 UTC  
> Url: https://github.com/boostorg/config/pull/394#discussion_r686208924  

>  if it's easier I could just omit that macro.  
  
Hard for me to judge.  
My suggestion is to ask the users what they feel. I would personally never use it since I consider it too tricky, but I am not really an expert library developer and I have no usecases for it.

> Username: Lastique  
> Created_at: 2022-08-14 14:43:58 UTC  
> Url: https://github.com/boostorg/config/pull/394#discussion_r945300768  

`__builtin_constant_p(n)` is not a constant expression, and it does not indicate whether `n` is a constant expression either. See [here](https://gcc.godbolt.org/z/3h1KP9oK8). What it shows is whether the compiler is able to deduce the value of `n` at compile time, including as a result of optimizations (e.g. inlining). This means that its value may change depending on optimization level used. If it returns `true`, `n` may not be a constant expression in C++ definition and thus may not be used in C++ constant expressions.  
  
I think `BOOST_IS_CONSTANT_EVALUATED_INT` needs to be renamed to avoid confusion with `std::is_constant_evaluated`. `__builtin_constant_p` is useful, but it is a very different thing.

---

📁 doc/macro_reference.qbk

```diff
1087 |+ [table
1088 |+ [[macro][Description]]
1089 |+ [[`BOOST_IS_CONSTANT_EVALUATED()`][Evaluated to `true` when the current context is `constexpr` and false otherwise.
```

> Username: libbooze  
> Created_at: 2021-08-09 23:06:41 UTC  
> Updated_at: 2021-08-09 23:12:08 UTC  
> Url: https://github.com/boostorg/config/pull/394#discussion_r685578079  

beginner question: why is this a macro?  
  
I think boost::is_constant_evaluated() would be a bit nicer, I presume this is for consistency with other stuff in this repo?

> Username: jzmaddock  
> Created_at: 2021-08-10 17:04:33 UTC  
> Updated_at: 2021-08-10 17:04:34 UTC  
> Url: https://github.com/boostorg/config/pull/394#discussion_r686160647  

We would need to include <type_traits> to cover that, and I'd very much like to NOT do that.  
  
Explanation: if it's a compiler about which we know nothing about, ie we don't know what it calls the compiler intrinsic that implements this, then we would need to be able to forward to std::is_constant_evaluated if it's presence is advertised in <version>.  
  
As it stands, it's up to users of these macros to include <type_traits> and everyone else can do without if they wish and only pay for what they use.  
  
We could of course put a real function into Boost.Core.

> Username: libbooze  
> Created_at: 2021-08-10 18:19:04 UTC  
> Url: https://github.com/boostorg/config/pull/394#discussion_r686215220  

Ah, I see, I was naively assuming that that feature macro is predefined, now I see it is in `<type_traits>`. Shame.


---

## Review 2 [Commented]

> Username: libbooze  
> Created_at: 2021-08-10 19:14:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/394#pullrequestreview-726750779  

📁 test/test_boost_is_constant_evaluated.cpp

```diff
   1 |+ //  (C) Copyright John Maddock 2021.
```

> Username: libbooze  
> Created_at: 2021-08-10 19:10:37 UTC  
> Updated_at: 2021-08-10 19:14:23 UTC  
> Url: https://github.com/boostorg/config/pull/394#discussion_r686250173  

Would it be good to add a "sanity test"?  
  
For some compiler you know it should have proper machinery in C++20 mode? I am not sure if boost tests have modern compilers running with -std=c++20, but I was thinking about something like this.  
  
https://www.godbolt.org/z/r9Y5x3x6M  
  
(use of assert just as an example, it can be a test framework assert_eq in real code)


---
