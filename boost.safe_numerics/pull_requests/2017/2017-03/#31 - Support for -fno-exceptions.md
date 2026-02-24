# #31 Support for -fno-exceptions [Closed]

> Username: akrzemi1  
> Created at: 2017-03-06 19:49:33 UTC  
> Updated at: 2019-10-24 16:28:57 UTC  
> Closed at: 2019-10-24 16:28:57 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/31  

Suppose I want to compile a program with exceptions disabled, on gcc/clang, I use flag `-fno-exceptions`. I can use a custom exception policy for `safe<int>` but that does not help me because in `-fno-exceptions` mode, compiler reports an error when it sees keyword `throw` anywhere, even in the policy I do not use. The solution for this is to use [Boost.ThrowException](http://www.boost.org/doc/libs/1_63_0/libs/exception/doc/boost_throw_exception_hpp.html) library.

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-04-08 22:27:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/31#issuecomment-292749957  

good call.  I looked at the Boost.ThrowException documentation.  It looks like one would have to explicitly set the correct macros for Boost.ThrowException to match the sense of the -fno-exceptions switch.  I just did a quick read of the docs on  Boost.ThrowException so I could be wrong.  I'm still looking into this.

---

## Comment 2

> Username: robertramey  
> Created_at: 2019-10-24 16:28:57 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/31#issuecomment-545998130  

The library has evolved since this PR was submitted so the PR won't fix the problem.  It is open as an issue though and I'm looking at it.

---
