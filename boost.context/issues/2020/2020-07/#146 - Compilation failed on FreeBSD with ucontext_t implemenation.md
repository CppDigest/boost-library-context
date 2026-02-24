# #146 - Compilation failed on FreeBSD with ucontext_t implemenation [Closed]

> Username: villytiger  
> Created at: 2020-07-06 12:53:49 UTC  
> Updated at: 2021-06-27 07:34:50 UTC  
> Closed at: 2021-06-27 07:34:50 UTC  
> Url: https://github.com/boostorg/context/issues/146  

On Linux stack_t is defined like this:  
```cpp  
typedef struct sigaltstack {  
	void *ss_sp;  
	int ss_flags;  
	size_t ss_size;  
} stack_t;  
```  
While on FreeBSD it is defined like this:  
```cpp  
typedef	struct {  
	char	*ss_sp;			/* signal stack base */  
	__size_t ss_size;		/* signal stack length */  
	int	ss_flags;		/* SS_DISABLE and/or SS_ONSTACK */  
} stack_t;  
```  
I observe the following error trying to compile boost::fiber for FreeBSD:  
```  
In file included from ./boost/context/fiber.hpp:8,  
                 from ./boost/fiber/context.hpp:28,  
                 from libs/fiber/src/context.cpp:7:  
./boost/context/fiber_ucontext.hpp: In instantiation of ‘boost::context::detail::fiber_activation_record* boost::context::detail::create_fiber2(boost::context::preallocated, StackAlloc&&, Fn&&) [with Ctx = boost::context::fiber; StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>&; Fn = std::_Bind<boost::context::fiber (boost::fibers::dispatcher_context::*(boost::fibers::dispatcher_context*, std::_Placeholder<1>))(boost::context::fiber&&)>]’:  
./boost/context/fiber_ucontext.hpp:408:45:   required from ‘boost::context::fiber::fiber(std::allocator_arg_t, boost::context::preallocated, StackAlloc&&, Fn&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>&; Fn = std::_Bind<boost::context::fiber (boost::fibers::dispatcher_context::*(boost::fibers::dispatcher_context*, std::_Placeholder<1>))(boost::context::fiber&&)>]’  
libs/fiber/src/context.cpp:45:105:   required from here  
./boost/context/fiber_ucontext.hpp:343:33: error: invalid conversion from ‘void*’ to ‘char*’ [-fpermissive]  
     record->uctx.uc_stack.ss_sp = stack_bottom;  
     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: olk  
> Created at: 2021-06-01 15:36:04 UTC  
> Url: https://github.com/boostorg/context/issues/146#issuecomment-852224460  

Could you provide a patch, please?

---

## Comment 2

> Username: olk  
> Created at: 2021-06-27 07:34:50 UTC  
> Url: https://github.com/boostorg/context/issues/146#issuecomment-869116674  

fixed by 'eb7c047 FreeBSD: type of stack_t::ss_sp is `char*`'
