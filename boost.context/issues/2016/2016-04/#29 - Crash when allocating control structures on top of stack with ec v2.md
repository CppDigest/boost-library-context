# #29 - Crash when allocating control structures on top of stack with ec v2 [Closed]

> Username: blastrock  
> Created at: 2016-04-11 13:41:13 UTC  
> Updated at: 2016-04-12 11:15:42 UTC  
> Closed at: 2016-04-12 06:11:22 UTC  
> Url: https://github.com/boostorg/context/issues/29  

I wanted to allocate the control structures on top of stack as demonstrated in the tutorial, but I couldn't find any complete example or test for that feature.  
  
I wrote this, which is mostly a copy paste from the tutorial:  
  
``` cpp  
#include <boost/context/execution_context_v2.hpp>  
  
using namespace boost::context;  
  
struct my_control_structure  
{  
  // captured context  
  execution_context<void> cctx;  
  
  template <typename StackAllocator>  
  my_control_structure(void* sp,  
                       std::size_t size,  
                       stack_context sctx,  
                       StackAllocator salloc)  
    : // create captured context  
      cctx(std::allocator_arg, preallocated(sp, size, sctx), salloc, [](auto ctx){return ctx;})  
  {  
  }  
};  
  
int main(int argc, char* argv[])  
{  
  // stack-allocator used for (de-)allocating stack  
  fixedsize_stack salloc(8192);  
  // allocate stack space  
  stack_context sctx(salloc.allocate());  
  // reserve space for control structure on top of the stack  
  void* sp = static_cast<char*>(sctx.sp) - sizeof(my_control_structure);  
  std::size_t size = sctx.size - sizeof(my_control_structure);  
  // placement new creates control structure on reserved space  
  my_control_structure* cs =  
      new (sp) my_control_structure(sp, size, sctx, salloc);  
  
  cs->cctx();  
  
  // destructing the control structure  
  // CRASH, the stack has already been freed  
  cs->~my_control_structure();  
  
  return 0;  
}  
```  
  
But this program crashes when compiled with ASan, because the stack is freed when the coroutine returns, so we can't call the destructor of my_control_structure, the pointer is already invalid.  
  
We can't call it just before the return statement either since the coroutine is still running, and not calling it at all would leak memory.

---

## Comment 1

> Username: olk  
> Created at: 2016-04-12 06:11:22 UTC  
> Url: https://github.com/boostorg/context/issues/29#issuecomment-208724081  

[](auto ctx){return ctx;} - the lambda executed by execution_context<void> simply returns, that means that the context has finished and the stack can be destructed/deallocated.  
  
modified code (adding an switch instead simply returning from lambda):  
[](auto ctx){ctx = ctx();return ctx;}  
...  
execution_context<void> cctx = cs->cctx(); // invoke context  
  
now your control structure can be destructed before the stack gets deallocated

---

## Comment 2

> Username: blastrock  
> Created at: 2016-04-12 10:49:54 UTC  
> Url: https://github.com/boostorg/context/issues/29#issuecomment-208843425  

Thanks for your answer!  
  
I'm not very concerned by this for the moment, but doesn't this impact performance? If I understand correctly, it involves 2 more context switches and one exception throw.   
  
And btw, if this is the recommended way, it would be nice to update the documentation to include it.

---

## Comment 3

> Username: olk  
> Created at: 2016-04-12 11:09:51 UTC  
> Url: https://github.com/boostorg/context/issues/29#issuecomment-208849835  

no - I fixed your example. if you simple return the context from your lambda, execution_context destructs the stack (because the context has finished, the stack is not longer required/used).  
From docu: 'On return the context-function of the current context has to specify an execution_context to which the execution control is transferred after termination of the current context. ...  If the context-function returns the stack will be destructed. '

---

## Comment 4

> Username: blastrock  
> Created at: 2016-04-12 11:15:42 UTC  
> Url: https://github.com/boostorg/context/issues/29#issuecomment-208851076  

Indeed, but I suggested improving the "allocating control structures on top of stack" example code to show how destruction should be done in that case.
