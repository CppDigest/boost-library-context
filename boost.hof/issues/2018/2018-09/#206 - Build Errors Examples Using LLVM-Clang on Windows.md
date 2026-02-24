# #206 - Build Errors Examples Using LLVM/Clang on Windows [Open]

> Username: adam-hartshorne  
> Created at: 2018-09-20 08:05:59 UTC  
> Updated at: 2018-11-28 22:39:16 UTC  
> Url: https://github.com/boostorg/hof/issues/206  

The following two bits of code compile ok with MSVC, but using LLVM/Clang produces the following errors.  
  
Using the code in print.cpp  
  
```  
BOOST_HOF_STATIC_FUNCTION(simple_print) = fix(first_of(  
    BOOST_HOF_LIFT(print_with_cout),  
    BOOST_HOF_LIFT(print_with_range),  
    BOOST_HOF_LIFT(print_with_tuple)  
));  
  
```  
produces the following error  
  
```  
1>./boost_hof_functions.h(45): error : templates cannot be declared inside of a local class  
1>C:\boost_1_68_0\boost/hof/lift.hpp(90):  note: expanded from macro 'BOOST_HOF_LIFT'  
1>C:\boost_1_68_0\boost/hof/lift.hpp(105):  note: expanded from macro 'BOOST_HOF_LIFT_CLASS'  
```  
  
And  
  
`BOOST_HOF_STATIC_LAMBDA_FUNCTION(print) = boost::hof::proj(simple_print);`  
  
produces  
  
```  
>./boost_hof_functions.h(51): error : non-const lvalue reference to type 'reveal_adaptor<...>' cannot bind to a temporary of type 'reveal_adaptor<...>'  
1>C:\boost_1_68_0\boost/hof/lambda.hpp(241):  note: expanded from macro 'BOOST_HOF_STATIC_LAMBDA_FUNCTION']  
```  
  
There are nearly identical errors with functions from in.cpp.

---

## Comment 1

> Username: pfultz2  
> Created at: 2018-09-20 16:48:07 UTC  
> Url: https://github.com/boostorg/hof/issues/206#issuecomment-423254074  

So it looks like clang on windows defines `_MSC_VER` on windows. This should be easy to fix for `BOOST_HOF_LIFT`.   
  
The other issues I am not sure what the error is. It seems related to #205 as well. Let me look into setting up clang for windows unless you have a dockerfile that sets it up already.

---

## Comment 2

> Username: adam-hartshorne  
> Created at: 2018-09-20 16:50:07 UTC  
> Url: https://github.com/boostorg/hof/issues/206#issuecomment-423254703  

Sorry I don't have a docker file.  
  
It is however extremely straight forward to drop LLVM/Clang into Visual Studio these days.

---

## Comment 3

> Username: pfultz2  
> Created at: 2018-09-20 23:23:11 UTC  
> Url: https://github.com/boostorg/hof/issues/206#issuecomment-423365336  

> It is however extremely straight forward to drop LLVM/Clang into Visual Studio these days.  
  
I dont have access to windows. Although, clang is portable and will run on any platform.

---

## Comment 4

> Username: adam-hartshorne  
> Created at: 2018-11-28 22:38:59 UTC  
> Updated at: 2018-11-28 22:39:16 UTC  
> Url: https://github.com/boostorg/hof/issues/206#issuecomment-442632468  

Was a fix ever found for this?
