# #69 - safe_range don't throw exceptions in VisualStudio17 [Closed]

> Username: Loggi-pro  
> Created at: 2019-02-01 06:30:29 UTC  
> Updated at: 2019-12-15 16:57:38 UTC  
> Closed at: 2019-12-15 16:57:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/69  

Using safe_signed_range and safe_signed_range in Visual Studio 17, i noticed that they dont throw any exception, when value going out of interval.   
For example:  
`safe_unsigned_range<0, 36> a =37;`  
compile and don't throw any exception with default exception policy.  
I tested **example5** in library, it also doesn't work.

---

## Comment 1

> Username: robertramey  
> Created at: 2019-02-01 06:39:54 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/69#issuecomment-459623330  

Hmmm - do you have another compiler - like the previous version of mdvc - to try?

---

## Comment 2

> Username: Loggi-pro  
> Created at: 2019-02-01 07:02:13 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/69#issuecomment-459627263  

I also have Visual Studio 2015 Update 2, but unfortunately it cannot compile this library, because it doesn't support multiple returns/switch-cases and so on in constexpr.

---

## Comment 3

> Username: robertramey  
> Created at: 2019-02-01 15:53:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/69#issuecomment-459768678  

I've had problems with different flavors of msvc compilers.  See https://www.boost.org/development/tests/develop/developer/safe_numerics.html  
One thing that seems to help is building for 64 bits with the 64 bit compiler.  Also explicitly setting std=C++14 I believe has solved some problems.  (You'll have to tech the exact syntax of the C++14 option.)  Keep me informed if you figure this out!

---

## Comment 4

> Username: robertramey  
> Created at: 2019-12-15 16:57:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/69#issuecomment-565826870  

I have investigated this example (and example 84) and discovered a bug which has not been fixed.  I also added to the test suite so this issue should not return.  Thanks for finding this.  As I write this, it is still in develop branch but will migrate to master after I get some test cycles in.  
Robert Ramey
