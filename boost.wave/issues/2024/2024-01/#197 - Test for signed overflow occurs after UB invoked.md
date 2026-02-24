# #197 - Test for signed overflow occurs after UB invoked [Closed]

> Username: jefftrull  
> Created at: 2024-01-14 20:55:29 UTC  
> Updated at: 2024-06-26 04:53:45 UTC  
> Closed at: 2024-06-26 04:53:44 UTC  
> Url: https://github.com/boostorg/wave/issues/197  

I noticed a warning in CI today for [two](https://github.com/boostorg/wave/blob/abe718abf4d9bdfceeaa0ec2f3115bcfb16bd695/include/boost/wave/grammars/cpp_expression_value.hpp#L189) [lines](https://github.com/boostorg/wave/blob/abe718abf4d9bdfceeaa0ec2f3115bcfb16bd695/include/boost/wave/grammars/cpp_expression_value.hpp#L256) in `cpp_expression_value.hpp`:  
  
`warning: assuming signed overflow does not occur when assuming that (X + c) < X is always false`  
  
It looks like we perform the calculation, and _then_ check to see if an overflow resulted. The compiler may discard the check (or worse).

---

## Comment 1

> Username: jefftrull  
> Created at: 2024-01-16 16:05:01 UTC  
> Url: https://github.com/boostorg/wave/issues/197#issuecomment-1894047002  

Note: I only see this warning in gcc version 7 and before. The release notes for gcc 8 _quietly_ note that `-Wstrict-overflow` is deprecated, although the documentation still lists it today, years later. At any rate the warning seems correct.

---

## Comment 2

> Username: jefftrull  
> Created at: 2024-01-16 18:52:51 UTC  
> Url: https://github.com/boostorg/wave/issues/197#issuecomment-1894330242  

Running the unit tests when compiled with `-fsanitize=signed-integer-overflow` will cause two failures

---

## Comment 3

> Username: jefftrull  
> Created at: 2024-02-02 17:26:03 UTC  
> Url: https://github.com/boostorg/wave/issues/197#issuecomment-1924351022  

Correction: three failures under gcc, four under Clang, thanks to what seems to be [a gcc bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=113726). However, it's basically invisible because these tests (`t_6_015` through `t_6_018`) are _supposed_ to fail anyway.
