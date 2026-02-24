# #387 - Error compiling samples [Closed]

> Username: andr1972  
> Created at: 2018-06-30 13:04:50 UTC  
> Updated at: 2025-05-10 01:54:07 UTC  
> Closed at: 2025-05-10 01:54:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/387  

First is simple error:  
in compiler.hpp and others hpp I must change:  
error_handler = function<ErrorHandler>(error_handler_)(  
                "Error! ", _2, phx::cref(error_handler_.iters)[_1]);  
to   
error_handler = function<ErrorHandler>(error_handler_)(  
                (char *const)("Error! "), _2, phx::cref(error_handler_.iters)[_1]);  
because is   
/usr/include/boost/phoenix/function/detail/cpp03/preprocessed/function_operator_10.hpp  
operator()(A0 const& a0 , A1 const& a1 , A2 const& a2) const  
        {  
            return detail::expression::function_eval<F, A0 , A1 , A2>::make(f, a0 , a1 , a2);  
        }  
  
Second error is worse:  
LLVM package changed and very interesting sample project conjure3 not compiling:  
can found   
#include <llvm/ExecutionEngine/ExecutionEngine.h>  
but can't  
#include <llvm/ExecutionEngine/JIT.h>

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-06-30 14:26:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/387#issuecomment-401544487  

What boost version do you use? The phoenix error should have been fixed by https://github.com/boostorg/phoenix/pull/58 (shipped with boost 1.67).  
  
The `conjure3` example (from what I see) uses LLVM JIT API that they dropped around 3.7 version. It should be possible to switch to MCJIT API without huge changes, but LLVM docs encourages new users to use ORC API.

---

## Comment 2

> Username: djowel  
> Created at: 2018-07-01 00:32:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/387#issuecomment-401575300  

It's been a while and we need to fix that somehow.

---

## Comment 3

> Username: andr1972  
> Created at: 2018-07-01 11:16:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/387#issuecomment-401600119  

I have boost 1.65 on Windows , on Linux Mint 19, I have installed boost (sudo apt-get install libboost-all-dev) is also 1.65.  
I have problem with LLVM, because I can't find LLVM user group, forum or mail list, only I can find LLVM developer group.

---

## Comment 4

> Username: saki7  
> Created at: 2025-05-10 01:54:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/387#issuecomment-2868215903  

Closing as stale. Feel free to reopen or open an another issue if you still have problems.
