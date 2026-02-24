# #57 - x64-Win32 SetUnhandledExceptionFilter does not work with make_fcontext/jump_fcontext [Closed]

> Username: jonasmr  
> Created at: 2017-04-27 08:54:59 UTC  
> Updated at: 2018-11-26 14:09:14 UTC  
> Closed at: 2018-01-16 04:48:16 UTC  
> Url: https://github.com/boostorg/context/issues/57  

On win32-x64, calling a function through jump_fcontext, our handler registered through SetUnhandledExceptionFilter is not called.  
  
I've made a project illustrating the problem:  
https://github.com/jonasmr/boost_context_seh  
  
Note, that it has to be launched without attaching the debugger, as the exception handler is never called when the debugger attached.  
  
Note that the application crashes, instead of printing "ExceptionHandler" to the console.  
  
Its using (jump/make)_x86_64ms_pe_masm.

---

## Comment 1

> Username: jonasmr  
> Created at: 2017-04-27 13:21:55 UTC  
> Updated at: 2017-04-27 13:25:02 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-297711315  

I tried doing the same thing with one of the samples, and it has the same problem - here is a compressed version of it:  
  
```  
#include <cstdlib>  
#include <iostream>  
#include <boost/context/continuation.hpp>  
LONG WINAPI XptHandler(struct _EXCEPTION_POINTERS* pInfo)  
{  
	printf("ExceptionHandler\n");  
	return EXCEPTION_EXECUTE_HANDLER;  
}  
void Crash()  
{  
	intptr_t* p = 0;  
	*p = 32;  
}  
namespace ctx = boost::context;  
ctx::continuation f1( ctx::continuation && c) {  
	Crash();  
    return std::move( c);  
}  
int main() {  
	SetUnhandledExceptionFilter(XptHandler);  
    ctx::continuation c;  
    int data = 1;  
    c = ctx::callcc( f1, data);  
	int x;  
	std::cin >> x;  
    return EXIT_SUCCESS;  
}  
```

---

## Comment 2

> Username: olk  
> Created at: 2017-04-27 18:06:15 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-297793626  

Could you check your example with Win32 please? Maybe the x64 version needs to preserve SEH too.  
It migh also be possible that some additional parts of the TEB/TIB must be preserved during context switch too (probably undocumented by MS).

---

## Comment 3

> Username: jonasmr  
> Created at: 2017-04-28 09:05:13 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-297947663  

I forgot to mention it but the version using callcc was actually using x86(I didnt get around to figuring out how to compile boost in x64).  
  
So it happens for both x64 and x86.  
  
In windbg, !exchain reports a valid exchain in x86:  
```  
0:000:x86> !exchain  
0000000000b2fe2c: ConsoleApplication1!__scrt_stub_for_is_c_termination_complete+e60 (0000000000a09e20)  
0000000000b2ff8c: ConsoleApplication1!__scrt_stub_for_is_c_termination_complete+df0 (0000000000a09db0)  
0000000000b2ffa8: ntdll_77b70000!FinalExceptionHandlerPad27+0 (0000000077bf2eeb)  
Invalid exception stack at ffffffffffffffff  
  
```  
  
But not in x64:  
```  
0:000> !exchain  
7 stack frames, scanning for handlers...  
Frame 0x02: error getting module for 00000242ceb92070  
Frame 0x03: error getting module for 0000000100000000  
Frame 0x04: error getting module for 000000000000808f  
Frame 0x05: error getting module for fdfdfdfd00000057  
Frame 0x06: error getting module for 0000008034dff980  
```  
  
I assume thats related to your comment about preserving SEH - Not sure if it helps as it still does not call the exception handler in either case.

---

## Comment 4

> Username: olk  
> Created at: 2017-04-28 11:35:45 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-297975858  

Strange, I assumed that x64 does table-based exception handling and doesn't require the x86's frame based exception handling (SEH chains on the stack). C++ exceptions are thrown and catched without problems in x64 Window apps.  
At the moment I've no solution - sorry.

---

## Comment 5

> Username: olk  
> Created at: 2017-04-30 16:45:49 UTC  
> Updated at: 2017-04-30 16:46:35 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-298242952  

Replace  
`SetUnhandledExceptionFilter(XptHandler);`  
by  
`AddVectoredExceptionHandler(1, XptHandler);`  
`XptHandler` gets called.

---

## Comment 6

> Username: jonasmr  
> Created at: 2017-05-01 05:57:18 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-298290778  

While its a proper workaround for the programs i provided, I think it is not a real fix.  
As I read it, AddVectoredExceptionHandler and SetUnhandledExceptionFilter serves two different purposes.  
  
Vectored gets called before handlers, Unhandled after.   
http://cbloomrants.blogspot.com/2011/11/11-09-11-weird-shite-about-exceptions.html  
http://stackoverflow.com/questions/28629351/is-addvectoredexceptionhandler-a-replacement-for-setunhandledexceptionfilter

---

## Comment 7

> Username: olk  
> Created at: 2017-05-01 07:08:05 UTC  
> Updated at: 2017-05-01 07:08:45 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-298296914  

I've no informations where the XptHandler will be stored if SetUnhandledExceptionFilter is used.  
I think it is not the TEB because only special parts like stack begin/end, fiber local storage data are exchanged by jump_fcontext. Remains only the stack, but it seams undocumented by MS (at least I didn't found a usefull information at MSDN).  
Do you have an idea?

---

## Comment 8

> Username: olk  
> Created at: 2017-05-01 09:10:18 UTC  
> Updated at: 2017-05-01 09:37:33 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-298308713  

as unit tests show, throwing and catching exceptions is working for 32bit/64bit - try/catch and __try/__except

---

## Comment 9

> Username: olk  
> Created at: 2017-05-01 09:13:49 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-298309065  

I confused that on 32bit '!exchain' shows a correct exception handler chain but not for x64.  
The question is if '!exchain' is permitted to e used on x64 (on x64 exception handler chain is not stored on the stack as done in i386).  
  
BTW, at least in MS VC 2015 '!exchain' is not accepted as a vailid command (immediate window).

---

## Comment 10

> Username: jonasmr  
> Created at: 2017-05-01 11:32:53 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-298321451  

I used !exchain in windbg, there it works on x64 as well.  
  
I dont have any suggestions as to what can be wrong ..

---

## Comment 11

> Username: olk  
> Created at: 2017-05-02 07:40:47 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-298530826  

I believe that the prologue in jump_fcontext needs some additional statements (save frame pointer) that could fix the problem.

---

## Comment 12

> Username: olk  
> Created at: 2018-01-16 04:48:16 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-357852903  

you could use WinFibers instead by applying property context-impl=winfib at b2 command line

---

## Comment 13

> Username: jonasmr  
> Created at: 2018-01-16 07:24:39 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-357874526  

Thanks for the workaround!  
  
Out of curiosity: Do you consider the problem to be unfixable?

---

## Comment 14

> Username: olk  
> Created at: 2018-01-16 08:07:56 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-357882706  

I believe it is fixable but I'm too busy + I don't use Windows (need for diving deeper into the Windows mechanisms).

---

## Comment 15

> Username: rxra  
> Created at: 2018-11-26 10:17:31 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-441587375  

>   
>   
> Thanks for the workaround!  
>   
> Out of curiosity: Do you consider the problem to be unfixable?  
  
hello @jonasmr , so yo succeed to have "SetUnhandledExceptionFilter " working now ?  
In our project we are using boost::coroutines2::coroutine and we have the same kind of issue : my crash handler is not working if crash hapened in coroutine. And I am a bit lost. I think it is related to "context switch" but cannot find what to do to correctly handle the crash. Do I need to setup my handler differently or do we badly use coroutine. Any help is welcome.

---

## Comment 16

> Username: olk  
> Created at: 2018-11-26 10:25:47 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-441589952  

You could build/use boost.context/boost.coroutine2 with context-impl=winfib in order to use Windows Fibers instead the assembler (MS does not provide enough infos which parts of TEB have to be copied during context switch)

---

## Comment 17

> Username: rxra  
> Created at: 2018-11-26 10:40:36 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-441594315  

ok i'll try this. thanks.

---

## Comment 18

> Username: olk  
> Created at: 2018-11-26 10:56:13 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-441598895  

as long as MS does not publish all relevant infos... yes

---

## Comment 19

> Username: rxra  
> Created at: 2018-11-26 11:01:10 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-441600331  

Hello again. At this time it is not clear for me where to make the change. Is it a compilation option for boost that I have to change, to use another context implementation. Or is it when I instantiate the coroutine that I have to setup it differently ?

---

## Comment 20

> Username: rxra  
> Created at: 2018-11-26 11:08:12 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-441602204  

ok I found something  
https://www.boost.org/doc/libs/1_68_0/libs/context/doc/html/context/ff/implementations__fcontext_t__ucontext_t_and_winfiber.html

---

## Comment 21

> Username: olk  
> Created at: 2018-11-26 12:57:49 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-441629763  

> ok I found something  
> https://www.boost.org/doc/libs/1_68_0/libs/context/doc/html/context/ff/implementations__fcontext_t__ucontext_t_and_winfiber.html  
  
correct - compile boost and your application with b2 property   
  
> context-impl=winfib

---

## Comment 22

> Username: rxra  
> Created at: 2018-11-26 14:09:14 UTC  
> Url: https://github.com/boostorg/context/issues/57#issuecomment-441650972  

Yeah. It seems it is working. Thanks.
