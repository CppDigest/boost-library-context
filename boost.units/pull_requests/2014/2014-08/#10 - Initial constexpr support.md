# #10 Initial constexpr support [Closed]

> Username: gnzlbg  
> Created at: 2014-08-28 14:39:40 UTC  
> Updated at: 2014-10-15 17:22:10 UTC  
> Closed at: 2014-10-14 15:39:56 UTC  
> Url: https://github.com/boostorg/units/pull/10  

Adds initial constexpr support to Boost.Units.  
  
There is some functionality that might only work on C++1z, C++14, and C++11 although most of the functionality works on C++11 and almost all of it works on C++14 (due to relaxed constexpr). Future work might offer C++11 compatible alternatives to relaxed constexpr.  
  
Most of the tests have been updated to be conditionally constexpr if support is available. Duplicating all test for constexpr is a maintenance nightmare. Boost.Test should offer a STATIC_CHECK_IF_CONSTEXPR macro that performs a static assert if the compiler supports constexpr or a normal CHECK otherwise. Future work might consider implementing such a macro within Boost.Units and using it where it makes sense.  
  
The radar beam height example has been updated to use constexpr. If the compiler supports C++11's constexpr the result is computed at compile-time and if the compiler also supports C++11's static_assert the result is also verified at compile-time.

---

## Comment 1

> Username: gnzlbg  
> Created_at: 2014-08-28 14:43:07 UTC  
> Url: https://github.com/boostorg/units/pull/10#discussion_r16843371  

This will only work if boost::math::isfinite is also a constexpr function. Boost.Units doesn't lose anything by declaring the functions within the cmath header constexpr since when used within a constexpr function they will just fail to compile. As soon as boost::math starts offering constexpr alternatives all of these will start to work.

---

## Comment 2

> Username: gnzlbg  
> Created_at: 2014-08-28 14:45:13 UTC  
> Url: https://github.com/boostorg/units/pull/10#issuecomment-53730060  

We can discuss here about adding a specific constexpr example to Boost.Units. But I now believe that the way to go is to make most of the Boost.Units examples constexpr on C++ > 11 compilers and fall back to run-time tests otherwise. However this will need some new testing functionality within Boost.Units.

---

## Comment 3

> Username: jhunold  
> Created_at: 2014-10-14 15:39:56 UTC  
> Url: https://github.com/boostorg/units/pull/10#issuecomment-59066621  

I won't merge this one as is. This patch has several issues:  
- The tests only compile with current clang (tested with llvm HEAD and -std=c++1y)  
- gcc-4.9 does not compile half of the tests in c++-1y mode, mostly complaining that "body of constexpr function ‘static constexpr ...’ not a return-statement".  
  
This seems to be the Boost.Config issue about different more or less relaxed constexpr support in different compilers. As long as this is not solved, I cannot approve this.  
  
And I doubt that using constexpr will show much performance gain in real world code. An optimising compiler will already perform a lot of simplification on constant based code like the examples, whereas real code will process variable data. So some benchmarking will be necessary to convince me, too.  
  
And last but not least: Please run the test suite next time with different compilers and C++-Standard settings. And note this in the PR comment field. This makes things easier for me.

---

## Comment 4

> Username: gnzlbg  
> Created_at: 2014-10-14 16:03:01 UTC  
> Url: https://github.com/boostorg/units/pull/10#issuecomment-59070390  

Boost.Config already has the relaxed constexpr macro merged in. I'll try to  
upgrade the patch to use the new macros as soon as I have time. This should  
fix the issues in C++11 mode (by disabling c++14 constexpr), as well as the  
issues with gcc (by disabling c++14 constexpr if it doesn't support it).  
The library should, however, be easy to backport to C++11 constexpr in case  
someone needs it.  
  
> And I doubt that using constexpr will show much performance gain in real  
> world code. An optimising compiler will already perform a lot of  
> simplification on constant based code like the examples, whereas real code  
> will process variable data. So some benchmarking will be necessary to  
> convince me, too.  
  
It is not about performance. It is about being able to use Boost.Units _at  
all_ within constexpr functions. The problem is that constexpr is  
transitive, so it requires everything you use within a constexpr function  
to be annotated with constexpr. Since Boost.Units is just a wrapper around  
literal types, and you can use those within constexpr functions, I don't  
think there is a good reason against conditionally annotating the whole  
library with constexpr so that those wishing to annotate their functions as  
constexpr can also use it.  
  
> And last but not least: Please run the test suite next time with different  
> compilers and C++-Standard settings. And note this in the PR comment field.  
> This makes things easier for me.  
  
Sorry about that. I didn't considered the effect of strict -std=c++11 mode  
on constexpr, which was a huge screw up on my part. Boost.Config should  
have made this better already. I cannot test different compilers (only have  
clang-trunk on my system), but I'll make sure to test with different  
standard settings.  
  
On Tue, Oct 14, 2014 at 5:39 PM, Jürgen Hunold notifications@github.com  
wrote:  
  
> Closed #10 https://github.com/boostorg/units/pull/10.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/units/pull/10#event-178298023.

---

## Comment 5

> Username: jhunold  
> Created_at: 2014-10-15 17:22:10 UTC  
> Url: https://github.com/boostorg/units/pull/10#issuecomment-59242352  

Okay, then I'm waiting for next patch. Some more thoughts:  
- if possible, commit smaller chunks for easier review and testing  
- think about using at least a second compiler. This makes development easier.  
  
I'm thinking about a separate development branch for this depending on the next patches. This would enable us to call in more testers more easily.  
  
Thanks for keeping this up.

---
