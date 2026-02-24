# #202 - Failed build with MSVC: error C2668 callcc: ambiguous call to overloaded function [Closed]

> Username: Saigut  
> Created at: 2022-07-06 15:33:37 UTC  
> Updated at: 2023-01-01 06:15:47 UTC  
> Closed at: 2022-09-18 14:04:30 UTC  
> Url: https://github.com/boostorg/context/issues/202  

Boost version: 1.72.0  
MSVC Version: 19.20.x  
Compiler error log:  
```  
xxx\include\boost/context/continuation_fcontext.hpp(325): error C2668: 'boost::context::callcc': ambiguous call to overloaded function  
xxx\include\boost/context/fiber_fcontext.hpp(212): note: could be 'boost::context::fiber boost::context::callcc<boost::context::fixedsize_stack,_Ty>(std::allocator_arg_t,StackAlloc &&,Fn &&)'  
        with  
        [  
            _Ty=cppt_co0::<lambda_b1a1exxxxx>,  
            StackAlloc=boost::context::fixedsize_stack,  
            Fn=cppt_co0::<lambda_b1a1exxxxx>  
        ]  
xxx\include\boost/context/continuation_fcontext.hpp(332): note: or 'boost::context::continuation boost::context::callcc<boost::context::fixedsize_stack,_Ty>(std::allocator_arg_t,StackAlloc &&,Fn &&)'  
        with  
        [  
            _Ty=cppt_co0::<lambda_b1a1exxxxx>,  
            StackAlloc=boost::context::fixedsize_stack,  
            Fn=cppt_co0::<lambda_b1a1exxxxx>  
        ]  
```  
This error maybe introduced from this commit https://github.com/boostorg/context/commit/10d3264f7b149078a3eab793e9a41cf5e3f5d00b#diff-05a991caec6cc800bb08467c92d4e2bf99390853e78b0c173247624b14978f16.  
The two `callcc` member functons in `fiber` class seem should to be deleted.

---

## Comment 1

> Username: olk  
> Created at: 2022-09-18 14:04:27 UTC  
> Url: https://github.com/boostorg/context/issues/202#issuecomment-1250315775  

ty
