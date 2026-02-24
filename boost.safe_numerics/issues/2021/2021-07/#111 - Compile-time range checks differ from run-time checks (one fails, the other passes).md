# #111 - Compile-time range checks differ from run-time checks (one fails, the other passes) [Open]

> Username: ttsiodras  
> Created at: 2021-07-23 18:07:38 UTC  
> Updated at: 2021-08-21 19:32:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/111  

Hello. I work in the European Space Agency, and have experience with the kinds of checks done by Ada and SPARK - and since I just met "safe_numerics", I did a quick test of the library with the following code:   
  
    #include <iostream>  
      
    #include <boost/safe_numerics/safe_integer.hpp>  
    #include <boost/safe_numerics/exception_policies.hpp>  
    #include <boost/safe_numerics/safe_integer_range.hpp>  
    #include <boost/safe_numerics/safe_integer_literal.hpp>  
      
    using namespace boost::safe_numerics;  
      
    using safe_t = safe_signed_range<  
        0, 10,  
        native,           // C++ type promotion rules work OK for this example  
    #ifdef COMPILE_TIME  
        loose_trap_policy // catch problems at compile time  
    #else  
        loose_exception_policy // catch problems at run-time  
    #endif  
    >;  
      
    template<int I>  
    #ifdef COMPILE_TIME  
    using const_safe_t = safe_signed_literal<I, native, loose_trap_policy>;  
    #else  
    using const_safe_t = safe_signed_literal<I, native, loose_exception_policy>;  
    #endif  
      
    safe_t f(safe_t x)  
    {  
        // fails to compile when we "gcc -DCOMPILE_TIME" - which is good! Adding 3 indeed drives us out of range  
        // return x + 3;  
      
        // fails to compile with "gcc -DCOMPILE_TIME".  Why?  
        return (const_safe_t<10>() - x) / const_safe_t<2>();  
    }  
      
    int main()  
    {  
        for(safe_t i=0; i<10; i++) {  
            std::cout << f(i) << std::endl;  
        }  
        std::cout << f(10) << std::endl;  
    }  
  
As the comments suggest, function `f` fails to compile when we enable compile-time checks (i.e. the `loose_trap_policy`) - but I don't see why....  `10-x` doesn't change the range (it remains 0..10 inclusive) and dividing by two gives a range from 0 to 5.  
  
To verify I am not missing something obvious, when compiling without `-DCOMPILE_TIME`, we use the run-time checks - and `main` goes through the entire range, without triggering a run-time exception (and prints the expected outputs).  
  
Is this a bug?

---

## Comment 1

> Username: robertramey  
> Created at: 2021-08-21 19:29:55 UTC  
> Updated at: 2021-08-21 19:32:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/111#issuecomment-903165369  

I've spend considerable time looking at this.  This has resulted in the decision to enhance the documentation on this subject.  Also it turns out that this behavior will be different for C++20+ so that needs to be demonstrated and documented as well.  Turns out that it's a subtle idea.  But for now, I'm going to answer your questions posed by your example.  
  
consider  `x + 3`.    
  
This is handled at compile time as `range[0,10] + range[numeric_limits<int>.min(),  numeric_limits<int>.max()]`.  C++ promotion will result in a result of type int.  It could be that this result exceeds numeric_limits<int>.max() . Since this could overflow, and the trap policy is specified, a compile time error is emitted and the parsing of the expression stops.  Note that C++ doesn't make the values of constants available at compile time*.  One might consider using an alternative expression `x + const_safe_t<3>` which would result in a type range[0, 13] which would be fine until we try to convert it to range[0,10] on return.  Since this is guaranteed to be doable, a "trap" will be invoked.  
  
If the exception policy is not "trap", the operation is considered legitimate but subject to checking during runtime.  In this case x + 3, having been promoted to int will not result in error, no runtime error will be detected ...  UNTIL the return value is covered to safe_t which has a range from 0 to 10 for some values of x.  At this point a runtime error is thrown.  All in all this seems like good behavior.  
  
consider `return (const_safe_t<10>() - x) / const_safe_t<2>();`  
  
On my configuration (mac os clang++ C++14) this is not failing under any mode.  
  
However,  `for(safe_t i = const_safe_t<0>(); i < const_safe_t<10>(); i++)` is invoking a compile-time trap.  The compiler doesn't "know" what value the `i ` is.  It only "knows" that the value is in the range[0,10] and that we are incrementing it.  So from this point of view, it _could_ overflow and hence invokes a compile-time "trap".  
  
The key distinction between the compile time trap where ranges are compared to see if some sort of overflow `could` occur and runtime exception where the actual value is available to be checked to see of some sort of overflow actually happened.  Granted it's a little bit confusing, but if one spends some time with it, it's clear that it can be no other way.  
  
This has come up before (see case study for embedded controller) and working around it is sort of hack.  This has convinced me to add a utility function safe_numerics::for(S & s, lambda...); to avoid having to re-do this workaround continually and thereby run the risk of getting it wrong.  
  
It's quite annoying that the compiler doesn't see the compile time value when it's available.  We should be able to write the following:  
  
`return (10 - x) / 2;`  
and  
`for(safe_t  i = 0; i < 10; i++) `  // except for the ++ part  
  
Well, with C++20 we will be able to !!!  this version has the function `is_constant_evaluated()` which will permit the library to know at compile time when the value is available and use that value rather than the range.  This might have great benefits in efficiency as the resulting ranges will be smaller and the code will be less cluttered with "const literal" ...  Look for this in the near future (if you're using C++20 or higher).  
  
It took me quite a bit of time to see all this.  But in the end, I didn't detect any errors in the library code.  The research led me to a better understanding, improvement of the manual, and understanding of the implications of C++20 in this context of safe numerics.
