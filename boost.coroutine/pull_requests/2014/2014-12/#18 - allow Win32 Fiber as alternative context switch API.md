# #18 allow Win32 Fiber as alternative context switch API [Closed]

> Username: microcai  
> Created at: 2014-12-06 08:04:25 UTC  
> Updated at: 2015-06-15 06:52:19 UTC  
> Closed at: 2015-06-05 18:14:21 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18  

Hi Oliver Kowalke,  
  
last weak, I report a bug that boost.context causes LoadLibrary to fail.  
And you response that might because boost.context didn't swap all TIB parts.  
  
Allowing alternative context switch API other than  reverse-engineered boost.context might solve the problem. So I speed some time to write the patch.  
  
users that wish to use Win32 API shoul define  BOOST_COROUTINE_USE_FIBER  
then users should be responsibe to make sure all threads that might call coroutine must be converted to fiber first.   
since IsThreadAFIber() is not available on some windows platform.  
  
if the user can make 100% sure that the platform he/she wish to support has IsThreadAFIber(), then define BOOST_COROUTINE_USE_IS_THREAD_A_FIBER  
and boost.coroutine will take care of converting to fiber and converting back to thread.

---

## Comment 1

> Username: microcai  
> Created_at: 2014-12-06 08:19:49 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-65889732  

Oh, I'm sorry. newly added file were tab indented.

---

## Comment 2

> Username: hyqhyq3  
> Created_at: 2014-12-06 13:46:20 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-65897412  

I'm using asio::spawn linking against 64bit libraries on windows 7, asio crushed when I create socket in coroutine.   
After this patch, it works.   
Stackful coroutine is a perfect solution for asio::async_\* operations.

---

## Comment 3

> Username: microcai  
> Created_at: 2014-12-09 03:18:19 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-66228932  

updated to fix bugs.

---

## Comment 4

> Username: tobias-loew  
> Created_at: 2014-12-12 08:01:16 UTC  
> Updated_at: 2014-12-12 08:02:02 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-66742512  

Hi,  
nice idea and code, I like it. But it crashes when used from a plain pull_coroutine: the placement new in [boost\coroutine\asymmetric_coroutine.hpp: 537] crashes since it operates on invalid memory (the null_stack_allocator doesn't allocate memory for sp, but it's given to the placement new).  
  
Tobias

---

## Comment 5

> Username: tobias-loew  
> Created_at: 2014-12-12 10:07:56 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-66753927  

Hi,  
  
after changing the basic_null_stack_allocator to the following, everything works fine.  
  
``` C++  
template< typename traitsT >  
struct basic_null_stack_allocator  
{  
    typedef traitsT traits_type;  
  
    static const size_t pull_coroutine_object_max_size = 1024;  
  
    void allocate(stack_context & ctx, std::size_t size_)  
    {  
        // just to avoid asserts, the stack is not used by Fiber anyway  
        ctx.size = size_;  
        // assume 1 K is enough for the pull_coroutine-object  
        ctx.sp = static_cast< char * >(calloc(pull_coroutine_object_max_size, 1)) + pull_coroutine_object_max_size;  
    }  
  
    void deallocate(stack_context & ctx)  
    {  
        void * limit = static_cast< char * >( ctx.sp) - pull_coroutine_object_max_size;  
        free(limit);  
    }  
};  
```  
  
Tobias

---

## Comment 6

> Username: tobias-loew  
> Created_at: 2014-12-12 10:35:29 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-66756662  

I just made a comment about this project on the boost-dev list:  
http://boost.2283326.n4.nabble.com/coroutine-x86-msvc11-stack-corruption-td4657990.html

---

## Comment 7

> Username: Jackarain  
> Created_at: 2015-05-08 21:21:09 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-100372112  

any update on this? boost.context 1.58 still fails on 64bit win7.

---

## Comment 8

> Username: olk  
> Created_at: 2015-06-05 18:14:21 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-109384351  

Implementation of execution_context (boost.context) will use Windows Fibers if compiler flag BOOST_USE_WINFIBERS is applied. boost.coroutine2 uses execution_context as building block (context switch).

---

## Comment 9

> Username: tobias-loew  
> Created_at: 2015-06-15 06:52:19 UTC  
> Url: https://github.com/boostorg/coroutine/pull/18#issuecomment-111954347  

First of all: thanks a lot for adding windows-fibers as engine for boost-context/coroutines.   
I have some suggestions on the implementation in boost/context/execution_context_winfib.ipp (master):  
  
line 91: "IsThreadAFiber" is available from Win-Vista upwards, thus the code won't compile on Visual-Studio with the "vc110_xp"-toolchain (a Win-XP workaround can be found here  
http://blogs.msdn.com/b/oldnewthing/archive/2004/12/31/344799.aspx)  
  
line 94: calling "ConvertFiberToThread();" seems to be the right thing to do here but it has two drawbacks.  
1. a performance penalty: every time when the "main"-thread switches to the other fiber a fiber-data-block is allocated and after return deleted. AFAI understood the msdn-docs you can allocate the fiber data once for a thread and reuse it and there is no need to free it after using  
1. for some strange reasons, when the called fiber loads COM-libraries everything works fine until ConvertFiberToThread is called on the original thread. Then all COM-calls return with an error indicating the COM-environment was not initialized (and calls to CoInitialize[Ex] have no effect). I got those errors when I called an ATL-webservice, which uses the COM-XLM-DOM-interface, from a coroutine.  
  
It would be great having a comipile- or runtime-time switch to use code like the following:  
  
```  
        if(IsThreadAFiber())  
        {  
            from->fiber_ = GetCurrentFiber();  
        }  
        else  
        {  
            from->fiber_ = ConvertThreadToFiber(nullptr);  
        }  
```  
  
or for the "vc110_xp"-toolchain  
  
```  
        from->fiber_ = ConvertThreadToFiber(nullptr);   
        if(!from->fiber_)   
        {   
            DWORD err = GetLastError();   
            if (err == ERROR_ALREADY_FIBER)   
            {  
                from->fiber_ = GetCurrentFiber();   
            }  
            else  
            {  
                // should never come here  
                // error handling  
            }  
        }  
```  
  
Tobias

---
