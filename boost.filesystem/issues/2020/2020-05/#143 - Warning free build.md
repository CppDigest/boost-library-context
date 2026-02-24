# #143 - Warning free build [Closed]

> Username: Flamefire  
> Created at: 2020-05-06 12:29:53 UTC  
> Updated at: 2020-05-06 16:22:00 UTC  
> Closed at: 2020-05-06 12:40:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143  

Would you mind activating `-Wall -Wextra -pedantic` with `-Werror` for CI? Can be done via Jam, see https://github.com/boostorg/nowide/blob/2e543e987d4e3c1e9960fb67166594d86dfba033/test/Jamfile.v2#L12-L20  
  
This avoids warnings for downstream users, like currently:  
```  
  
/home/runner/work/nowide/nowide/libs/filesystem/include/boost/filesystem/operations.hpp:59:26: error: comma at end of enumerator list [-Werror=pedantic]  
   overwrite_existing = 1u,      // Overwrite existing file  
  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2020-05-06 12:41:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624625749  

I don't support warning-free builds, especially not in "warnings as errors" mode.  
  
I will fix reasonable warnings, like this one, upon report.

---

## Comment 2

> Username: Flamefire  
> Created at: 2020-05-06 12:59:45 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624634028  

Ok, but wouldn't it be easier to have those on CI instead of waiting for a report? Especially for releases reports might be to late. The "warnings as errors" mode is just so it can be caught on CI

---

## Comment 3

> Username: Lastique  
> Created at: 2020-05-06 14:58:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624700667  

I don't want a CI broken by a warning. Especially, when a new compiler comes out with a bunch of new warnings.

---

## Comment 4

> Username: Flamefire  
> Created at: 2020-05-06 15:20:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624713591  

I don't see anything bad by that. Warnings *may* be bugs (although most often they are not) so being forced to fix or ignore a particular warning is a good thing. And downstream users will not face that warning later on either.  
  
And "when a new compiler comes out": It isn't like this is going to break your CI out of a sudden. You got to  actively add that compiler to CI. And then new warnings mean new kinds of errors can be detected. Example: Moved-from usage detection was added not so long ago.

---

## Comment 5

> Username: Lastique  
> Created at: 2020-05-06 15:31:19 UTC  
> Updated at: 2020-05-06 15:31:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624719995  

> although most often they are not  
  
Exactly. In my experience, warnings are often nuisance rather than a helper. Sometimes, you have to cripple the code instead of improving it to get rid of the warning. I will recommend users to disable such warning.  
  
BTW, I don't consider littering code with `#pragma`s improving it either. I understand it makes user experience better, and that is the only reason I would consider adding yet another one to silence a warning. But I'm not going to waste my time to actively test and pre-emptively catch and silence every warning on every compiler. I have other things to do. I hope you understand.

---

## Comment 6

> Username: Flamefire  
> Created at: 2020-05-06 15:42:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624726270  

Sure I understand. Been through that already and I know it is some work in the beginning. And some warnings (especially MSVC) annoy the sh*t out of me but I decided to bite the bullet and silence warnings so real issues don't stay undetected. It also makes you reconsider the code which it warns about and sometimes you find a real issue related to the warning. IMO that is worth the struggle.  
  
Example worth checking:  
```  
libs/filesystem/src/operations.cpp:1032:30: warning: missing initializer for member ‘stat::st_dev’ [-Wmissing-field-initializers]  
   struct ::stat from_stat = {};  
```  
Could be a real issue.   
  
In the end it's your decision just wanted to bring up the arguments although I guess you know them already ;)

---

## Comment 7

> Username: Lastique  
> Created at: 2020-05-06 15:50:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624731009  

> Example worth checking  
  
That is an example of a bogus warning, as the code clearly zero initializes all members, as required by the standard. That is the intention of the code and trying to fix it does not improve the code - quite the opposite.

---

## Comment 8

> Username: Flamefire  
> Created at: 2020-05-06 16:05:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624739612  

> trying to fix it does not improve the code - quite the opposite.  
  
I'm quite advanced in C++ and would still need to look up all 300 or so initializations that can happen to be 100% sure this is zero-initialization. So I wouldn't be sure what that code does from a first look. While a `struct ::stat from_stat; memset(..., 0)` would be very clear. So for me it would improve the code. Just as an example that also "improving" is relative to the reader.

---

## Comment 9

> Username: Lastique  
> Created at: 2020-05-06 16:22:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/143#issuecomment-624748776  

> While a `struct ::stat from_stat; memset(..., 0)` would be very clear.  
  
Right, and while doing that you'll forget the `std::` qualification or include `cstring`. Or mess up the size, if the variable is an array. So many things can go wrong with `memset` and friends... :) I'll prefer the built-in initialization syntax - it is shorter, it's safer, and it doesn't add new dependencies. And as long as I'm dealing with the code, its semantics is clear.
