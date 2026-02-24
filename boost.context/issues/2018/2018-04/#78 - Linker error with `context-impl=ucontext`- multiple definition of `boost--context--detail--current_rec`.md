# #78 - Linker error with `context-impl=ucontext`: multiple definition of `boost::context::detail::current_rec` [Closed]

> Username: jspam  
> Created at: 2018-04-10 10:42:05 UTC  
> Updated at: 2018-04-11 09:25:36 UTC  
> Closed at: 2018-04-11 04:24:25 UTC  
> Url: https://github.com/boostorg/context/issues/78  

When building with `context-impl=ucontext define=BOOST_USE_UCONTEXT` on branch develop (commit 7900c869) I get the following linker error:  
  
```  
bin.v2/libs/context/build/gcc-7.3.0/release/context-impl-ucontext/threading-multi/fiber.o:(.tbss+0x18): multiple definition of `boost::context::detail::current_rec'  
bin.v2/libs/context/build/gcc-7.3.0/release/context-impl-ucontext/threading-multi/continuation.o:(.tbss+0x18): first defined here  
collect2: error: ld returned 1 exit status  
  
    "g++"    -o "bin.v2/libs/context/build/gcc-7.3.0/release/context-impl-ucontext/threading-multi/libboost_context.so.1.67.0" -Wl,-h -Wl,libboost_context.so.1.67.0 -shared -Wl,--start-group "bin.v2/libs/context/build/gcc-7.3.0/release/context-impl-ucontext/threading-multi/continuation.o" "bin.v2/libs/context/build/gcc-7.3.0/release/context-impl-ucontext/threading-multi/fiber.o" "bin.v2/libs/context/build/gcc-7.3.0/release/context-impl-ucontext/threading-multi/execution_context.o" "bin.v2/libs/context/build/gcc-7.3.0/release/context-impl-ucontext/threading-multi/posix/stack_traits.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread   
```  
  
The multiple definitions are here:  
  
`fiber.cpp:24: thread_local fiber_activation_record * current_rec;`  
`continuation.cpp:26: thread_local activation_record * current_rec;`

---

## Comment 1

> Username: olk  
> Created at: 2018-04-11 04:24:25 UTC  
> Url: https://github.com/boostorg/context/issues/78#issuecomment-380322972  

ty

---

## Comment 2

> Username: olk  
> Created at: 2018-04-11 09:13:56 UTC  
> Url: https://github.com/boostorg/context/issues/78#issuecomment-380384166  

could you verify the fix, please?

---

## Comment 3

> Username: jspam  
> Created at: 2018-04-11 09:25:36 UTC  
> Url: https://github.com/boostorg/context/issues/78#issuecomment-380387512  

It works, thank you for the fix.
