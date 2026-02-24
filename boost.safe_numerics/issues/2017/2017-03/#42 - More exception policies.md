# #42 - More exception policies [Open]

> Username: akrzemi1  
> Created at: 2017-03-14 17:29:49 UTC  
> Updated at: 2018-10-17 17:00:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/42  

The ones that have been suggested:  
  
1. a policy with a uniform, guaranteed, compiler-friendly UB, like `__builtin_unreachable`.  
2. a policy that calls `std::terminate()` (and enables the inspection of the core or launches debugger.

---

## Comment 1

> Username: muggenhor  
> Created at: 2018-10-15 08:52:38 UTC  
> Updated at: 2018-10-15 09:25:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/42#issuecomment-429761240  

About (1): you probably want `__builtin_trap()` instead. Both GCC and Clang turn that into the `ud2` instruction (on x86-64). `__builtin_unreachable` is just an optimization hint: it tells the compiler that control flow should never be able to reach that code in a well behaved program. `__builtin_trap` is slightly less strong, it informs the compiler that it's an error to reach that location and that it should terminate if it does. Or maybe you want a choice of these policies. See: https://godbolt.org/z/Tr5ZAr  
  
An additional policy that I would find interesting is a "tainting" policy. I.e. upon overflow, wrap-around and truncation produce a special "tainted" value that would behave similar to a NaN for floating point. Thus when using this in arithmetic expressions it would make the result of those expressions be tainted as well.  
  
Specifically I'm thinking of having a way to produce a mixture of `std::expected`'s (or outcome's) and `safe`'s interfaces. I'd expect arithmetic, bitwise and assignment operators to stay usable with, as only behavioral difference, taint propagation. Comparison and conversion operators require a definite value and as such those should probably be the locations to invoke some failure handler if tainted. Even when comparing two `expected_safe<I>`s: two tainted values should not be considered equal, just like two NaNs always compare false, regardless of the comparison operator used.  
  
If I have some time I'll do some experimentation to figure out how much effort this is to build on top of this library. But any guidance and suggestions would be appreciated.

---

## Comment 2

> Username: robertramey  
> Created at: 2018-10-15 15:10:02 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/42#issuecomment-429892957  

(1) - I'm not referring to the _builtin... you mention.  I'm referring to builtins for extended integer arithmetic. see https://gcc.gnu.org/onlinedocs/gcc/Integer-Overflow-Builtins.html  
  
(2) "tainting" policy.  This is implemented in the library as named "checked result"  It is well explained in the documentation.  It is also described as an "extended integer type". This is the fundamental building block in the higher level overloading of the C/C++ arithmetic operators. It is implemented as a result of all C/C++ integer operations.  It's not demonstrated in the documentation, but this "checked result" type can be used exactly as you desire.  I actually can envision someone who might want to use it in a C program to implement safe operations without overloading the C++ operators.  But I think you'd need a C++ compiler anyway since the current implementation of checked_result depends on other C++ facilities beyond operator overloading.  
  
"checked result" IS a variation of "expected, outcome, variant or whatever"  The discussion of these things has gone way over the top and off the rails.  The key feature of "checked result" is that it cannot be created without a value and is immutable.  This makes it efficient for my purposes and eliminates all the issues with variants which are not immutable.  
  
If you read the documentation section on this and look into the code - you'll find it very, very interesting.  It is a very subtle, powerful and non-obvious implementation feature of the library and likely has application way beyond safe numerics.  But too busy right now to save the whole world.  
  
In short, what you want is already in there as a lower level.  
  
I can tell you're pretty perceptive and "get" the library.  I think you find it very illuminating and enjoyable to delve deeper and see what you can find.  maybe you might want to experiment with implementing "checked result" for floats/doubles.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2018-10-17 17:00:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/42#issuecomment-430708137  

> (1) - I'm not referring to the _builtin... you mention. I'm referring to builtins for extended integer arithmetic. see [https://gcc.gnu.org/onlinedocs/gcc/Integer-Overflow-Builtins.html](https://gcc.gnu.org/onlinedocs/gcc/Integer-Overflow-Builtins.html)  
  
@muggenhor mentions `__builtin_trap()` for a different reason. `__builtin_trap()` is like a keyword in gcc and clang for triggering UB, but in a way that is easily diagnosable by the tools. Thus, you could change `unsigned int`'s wrap-around functionality into a uniform UB, which can be then diagnosed by e.g., UB-sanitizer.
