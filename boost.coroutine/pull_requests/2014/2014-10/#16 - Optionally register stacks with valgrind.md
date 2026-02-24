# #16 Optionally register stacks with valgrind [Merged]

> Username: anttirt  
> Created at: 2014-10-06 13:25:22 UTC  
> Updated at: 2014-10-06 16:53:52 UTC  
> Merged at: 2014-10-06 16:53:52 UTC  
> Closed at: 2014-10-06 16:53:52 UTC  
> Url: https://github.com/boostorg/coroutine/pull/16  

Running programs that switch stacks under valgrind causes valgrind to spit warnings like the following:  
  
```  
==5234== Warning: client switching stacks?  SP change: 0xffefffb90 --> 0x5c4b158  
==5234==          to suppress, use: --max-stackframe=68605921848 or greater  
```  
  
The `VALGRIND_STACK_[DE]REGISTER` macros tell valgrind to treat the selected memory regions as stack space which suppresses those errors. The macros result in a magic instruction sequence that valgrind recognizes and they don't cause any link dependency.  
  
One concern is the `<valgrind/valgrind.h>` header because to build with `valgrind=on` it has to be externally available. Valgrind's headers have a permissive license so they could in theory be distributed along with boost but I'm not sure about the details of including code from other projects in boost.  
  
Also, I'm not sure how to write tests for this since it's a library project. If there's a mechanism for checking executable output then it should be easy enough to write a trivial program that switches stacks once and checks the valgrind output for the above warning.

---

## Comment 1

> Username: olk  
> Created_at: 2014-10-06 16:53:46 UTC  
> Url: https://github.com/boostorg/coroutine/pull/16#issuecomment-58048777  

very nice - thx

---
