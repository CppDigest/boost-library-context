# #62 - Naive question about atomic_sp_constexpr_test.cpp [Closed]

> Username: dkrejsa  
> Created at: 2019-01-22 00:38:40 UTC  
> Updated at: 2019-01-23 00:36:33 UTC  
> Closed at: 2019-01-22 04:31:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/62  

Hi,  
I'm not much more than a C++ beginner, but I'm involved in the effort to get Boost supported better on VxWorks.  One of the tests that is failing across all our targets is the atomic_sp_constexpr_test.  
  
In that test there are static objects created:  
  
    static Z z;  
  
    static boost::atomic_shared_ptr<X> p1;  
  
Further, the constructor Z() assigns to the static object p1:  
  
    Z::Z()  
    {  
        p1 = boost::shared_ptr<X>( new X );  
    }  
  
Now, it seems this test is failing because by the time main() runs, p1 has been zeroed.  
I think that since z is defined before p1, the constructor for z runs before the constructor for  
p1; Z::Z() assigns to the un-initialized p1.  But then the constructor for p1 runs and reinitializes  
p1. (In fact, on an Intel target, immediately after the call to Z::Z() are some move instructions  
that zero p1.)  
  
This all runs before main().  In main(), a new shared pointer p2 is set equal to p1, and the  
test expects that p2 contains a non-nullptr pointer, and that p2.use_count is 2.  But since p1 has  
been zeroed, this fails.  
  
So, I'm not sure how this test is supposed to work, and also, how is it related to _constexpr_ ?  
  
We're building with llvm 7, and/or gnu 8.1.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-01-22 01:08:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/62#issuecomment-456237232  

It's testing whether the initialization of `p1` is static (not performed at runtime.)  
  
Such initialization is performed in C++11 when the constructor is `constexpr`.  
  
What standard library are you using?

---

## Comment 2

> Username: dkrejsa  
> Created at: 2019-01-22 04:04:09 UTC  
> Updated at: 2019-01-22 04:12:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/62#issuecomment-456264298  

We use the Dinkum C/C++  library, as ported to VxWorks 7.  
Thanks, I'll look into reasons that boost::atomic_shared_ptr<x> might not be constexpr.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-01-22 04:27:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/62#issuecomment-456267349  

The usual reason for this test failing, but `sp_constexpr_test.cpp` succeeding, is that the constructor of `std::atomic_flag` isn't `constexpr`.  
  
Since you use Clang, you can add `[[clang::require_constant_initialization]]` before the definition of `p1` and the compiler will tell you why the initialization isn't performed at compile time.

---

## Comment 4

> Username: dkrejsa  
> Created at: 2019-01-22 04:31:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/62#issuecomment-456267918  

Wow, thank you very much!

---

## Comment 5

> Username: pdimov  
> Created at: 2019-01-22 04:31:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/62#issuecomment-456267941  

This test fails on popular compilers such as MSVC and on popular standard libraries such as libc++ (as you can see from the ifdefs in it), so it's not a big issue if it fails for VxWorks too.

---

## Comment 6

> Username: dkrejsa  
> Created at: 2019-01-23 00:36:33 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/62#issuecomment-456620464  

Hi pdimov,  
I added [[clang::require_constant_initialization]] as you suggested, and indeed the resulting error message pointed at the constructor for std::atomic_flag not being constexpr.  
(Simply adding constexpr to the std::atomic_flag constructor definition made the test pass.  I don't see a reason why the constructor shouldn't be constexpr, but I'm a neophyte.  I'll try to ask someone locally who knows a bit more than me.)   In any case, thanks again.
