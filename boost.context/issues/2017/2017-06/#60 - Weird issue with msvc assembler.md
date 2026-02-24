# #60 - Weird issue with msvc assembler [Closed]

> Username: blitz-research  
> Created at: 2017-06-13 02:49:56 UTC  
> Updated at: 2017-06-13 07:35:32 UTC  
> Closed at: 2017-06-13 04:34:56 UTC  
> Url: https://github.com/boostorg/context/issues/60  

Hi,  
  
I have a project that is built with msvc (express 2015) via cl, ml, link etc tools from the command line, and was having all sorts of problems getting boost context code to work with it - make_fcontext was just crashing everytime.  
  
Finally narrowed it down to the stack layout being messed up - the first parameter passed to make_fcontext was not located at [esp+4] but at [esp+8]!  
  
Finally narrowed that down to the 'PROC' declaration in the ASM:  
  
make_fcontext PROC BOOST_CONTEXT_EXPORT  
  
I have no idea what the 'BOOST_CONTEXT_EXPORT' bit is supposed to do, but removing it fixed the problem entirely. I also removed it from jump_context and ontop_context (whatever that does!).  
  
It may well be I'm missing an ML option on the command line or something as I would have assumed people were using this with msvc all over the place. Anyone know what's up?  
  
Love the low level boost::context stuff by the way and have used it on a ton of targets. Would love to see it in WASM too eventually - please talk the WASM guys into it!  
  
Bye!  
Mark

---

## Comment 1

> Username: olk  
> Created at: 2017-06-13 04:34:56 UTC  
> Url: https://github.com/boostorg/context/issues/60#issuecomment-308004307  

Probably you used make_fcontext in the wrong way - please note that make_fcontext/jump_fcontext is a private API, use calcc() instead.

---

## Comment 2

> Username: olk  
> Created at: 2017-06-13 06:33:20 UTC  
> Updated at: 2017-06-13 06:34:24 UTC  
> Url: https://github.com/boostorg/context/issues/60#issuecomment-308020766  

'PROC' is a macro and works only for MASM (== Macro ASM), it is specific for Microsoft's assembler.  
'PROC' is used in file like make_x86_64_ms_pe_masm.asm.  
If you use GAS you have to select make_x86_64_ms_pe_gas.asm.

---

## Comment 3

> Username: blitz-research  
> Created at: 2017-06-13 07:01:43 UTC  
> Url: https://github.com/boostorg/context/issues/60#issuecomment-308025985  

I think I understand what's up now.  
  
The main problem was that I hadn't defined BOOST_CONTEXT_EXPORT, which for  
reasons I don't quite understand (but aren't going to lose any sleep over)  
was causing ML (what masm's called these days) to insert some weird prolog  
stuff into the ASM code, which was messing up the stack frame.  
  
By defining BOOST_CONTEXT_EXPORT to be 'nothing' (or removing it entirely  
as I did) everything started working.  
  
The context/fiber ASM code is very robust - I'm using it on windows (mingw  
and now msvc too), macos, linux, raspberry pi, android (32/64 bit) and ios  
(32/64 bit) - which is why I was a bit surprised to have any problems with  
it.  
  
Thanks for looking into it though!  
  
  
On Tue, Jun 13, 2017 at 6:33 PM, Oliver Kowalke <notifications@github.com>  
wrote:  
  
> 'PROC' is a macro and works only for MASM (== Macro ASM), it is specific  
> for Microsoft's assembler  
> 'PROC' is used in file like make_x86_64_ms_pe_masm.asm.  
> If you use GAS you have to select make_x86_64_ms_pe_gas.asm-.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/context/issues/60#issuecomment-308020766>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ADU3QgAfbcdck7rUnRMz_SXLOtAohI92ks5sDi0wgaJpZM4N36-g>  
> .  
>

---

## Comment 4

> Username: olk  
> Created at: 2017-06-13 07:21:39 UTC  
> Url: https://github.com/boostorg/context/issues/60#issuecomment-308029915  

BOOST_CONTEXT_EXPORT is defined in build/Jamfile.v2:  
<toolset>msvc,<link>shared:<define>BOOST_CONTEXT_EXPORT=EXPORT  
It is required because the symbol 'make_fcontext' must be exported (because boost.context is a library).

---

## Comment 5

> Username: blitz-research  
> Created at: 2017-06-13 07:35:31 UTC  
> Url: https://github.com/boostorg/context/issues/60#issuecomment-308032834  

Aha, OK, thanks.  
  
  
On Tue, Jun 13, 2017 at 7:21 PM, Oliver Kowalke <notifications@github.com>  
wrote:  
  
> BOOST_CONTEXT_EXPORT is defined in build/Jamfile.v2:  
> msvc,shared:BOOST_CONTEXT_EXPORT=EXPORT  
> It is required because the symbol 'make_fcontext' must be exported  
> (because boost.context is a library).  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/context/issues/60#issuecomment-308029915>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ADU3QkE5Og0hlm_QY-ydKj8u2P7WP2cYks5sDjiEgaJpZM4N36-g>  
> .  
>
