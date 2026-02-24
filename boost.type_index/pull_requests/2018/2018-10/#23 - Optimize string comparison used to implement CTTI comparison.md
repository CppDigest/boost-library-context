# #23 Optimize string comparison used to implement CTTI comparison [Merged]

> Username: Lastique  
> Created at: 2018-10-28 00:08:09 UTC  
> Updated at: 2018-12-23 14:46:52 UTC  
> Merged at: 2018-12-23 09:27:44 UTC  
> Closed at: 2018-12-23 09:27:44 UTC  
> Url: https://github.com/boostorg/type_index/pull/23  

When the strings are known to be runtime objects, use compiler builtin  
for strcmp for comparing strings. The compiler will generate a runtime  
call for the (presumably, well-optimized) strcmp instead of rolling  
a local per-character comparison loop. When the builtin is not available  
or we cannot detect compile-time strings, use the local loop as before.  
  
Also, when C++14 constexpr is not available, use strcmp right away,  
since there is no use for the local loop in this case.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2018-10-28 10:01:22 UTC  
> Url: https://github.com/boostorg/type_index/pull/23#issuecomment-433691328  

I'm afraid this won't work. `__builtin_constant_p` has limitations:  
  
>  However, if you use it in an inlined function and pass an argument of the function as the argument to the built-in, GCC never returns 1 when you call the inline function with a string constant or compound literal (see Compound Literals) and does not return 1 when you pass a constant numeric value to the inline function unless you specify the -O option.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-10-28 15:28:29 UTC  
> Url: https://github.com/boostorg/type_index/pull/23#issuecomment-433715444  

But it does work, the tests pass in debug mode and no optimization with gcc 8.2 at least. The quoted piece of documentation is not quite correct wrt. constexpr functions, which have to operate on compile-time data regardless of optimization level.  
  
BTW, this is about how constexpr `std::char_traits` are implemented in libstdc++ in C++17 mode.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2018-10-30 08:04:04 UTC  
> Url: https://github.com/boostorg/type_index/pull/23#issuecomment-434206554  

> BTW, this is about how constexpr std::char_traits are implemented in libstdc++ in C++17 mode.  
  
You are right. I see close code in libstdc++ https://github.com/gcc-mirror/gcc/blob/fab58cd07f8ecf24d936dc352e4f79fddd57f63f/libstdc%2B%2B-v3/include/bits/char_traits.h#L246  
  
But the patch still makes me nervous. I'd like to double check that at least GCC and Clang eliminate unnecessary loops for constant checking. Give me some time to play with godbolt and I'll merge the PR if there are no issues.

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-12-12 19:38:46 UTC  
> Url: https://github.com/boostorg/type_index/pull/23#issuecomment-446716756  

So, did you have a chance to test the patch?

---

## Comment 5

> Username: apolukhin  
> Created_at: 2018-12-23 14:46:52 UTC  
> Url: https://github.com/boostorg/type_index/pull/23#issuecomment-449641013  

Many thanks for the PR!

---
