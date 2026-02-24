# #239 - CMake builds fails on QNX (qcc): Incorrect assembler invocation [Closed]

> Username: Felix-El  
> Created at: 2023-09-06 11:39:13 UTC  
> Updated at: 2023-10-01 14:30:13 UTC  
> Closed at: 2023-10-01 14:30:12 UTC  
> Url: https://github.com/boostorg/context/issues/239  

While trying to build Boost for QNX with CMake, I faced the issue that `-A,--defsym` assmbler flags were not accepted by qcc.  
Only when I replaced all defines without assignment.  
  
```cmake  
target_compile_definitions(boost_context VAR)  
```  
into  
  
```cmake  
target_compile_definitions(boost_context VAR=)  
```  
  
would the build succeed. I did not face the issue while building with `b2`.  
Would it be possible to either really apply this approach and/or watch more closely which definitions are passed to the assembler?
