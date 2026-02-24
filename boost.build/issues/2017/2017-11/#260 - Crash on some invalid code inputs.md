# #260 - Crash on some invalid code inputs [Closed]

> Username: jpcima  
> Created at: 2017-11-12 03:49:49 UTC  
> Updated at: 2017-12-16 22:19:21 UTC  
> Closed at: 2017-12-16 22:19:21 UTC  
> Url: https://github.com/boostorg/build/issues/260  

There is a null pointer error when one attempts to instantiate a class which does not define the __init__ member.  
```  
import "class" : new ;  
class a {  
  #rule __init__ ( ) { }  
}  
new a ;  
```  
This is the top of the gdb stack trace.  
```  
#0  0x00007ffff6ec78a0 in raise () from /usr/lib/libc.so.6  
#1  0x00007ffff6ec8f09 in abort () from /usr/lib/libc.so.6  
#2  0x00005555556192dc in function_rulename (function=0x0) at function.c:2928  
#3  0x000055555563c5e7 in function_call_member_rule (function=0x555556491f10, frame=0x7ffffffe2f60,   
    s=0x5555563bedd8 <stack_global.result>, n_args=19, rulename=0x5555563ed9f0, file=0x555556497a78, line=90) at function.c:536  
#4  0x0000555555637255 in function_run (function_=0x555556491f10, frame=0x7ffffffe2f60, s=0x5555563bedd8 <stack_global.result>)  
    at function.c:4603  
#5  0x0000555555605160 in evaluate_rule (rule=0x55555695aa98, rulename=0x5555563f4500, frame=0x7ffffffe2f60) at compile.c:150  
#6  0x000055555563c0e4 in function_call_rule (function=0x5555567a56c0, frame=0x7ffffffe7280,   
    s=0x5555563bedd8 <stack_global.result>, n_args=1, unexpanded=0x5555563f4500 "new", file=0x5555564392c0, line=6)  
    at function.c:493  
```  
It happens because of null rule->procedure in this [context](https://github.com/boostorg/build/blob/develop/src/engine/function.c#L536). My version is 2bb392f60cf33f8590049469571767341cb21e62 on develop.
