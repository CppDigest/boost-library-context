# #25 - Is it possible to detect architecture as being AMD and not anything else ? [Closed]

> Username: jfalcou  
> Created at: 2015-06-25 19:27:41 UTC  
> Updated at: 2015-07-13 13:35:35 UTC  
> Closed at: 2015-07-13 13:35:35 UTC  
> Url: https://github.com/boostorg/predef/issues/25  

When dealing with SIMD instructions stes, we need to know if a CPU is an actual AMD to acces SSE4A intrinsics. Currently AMD detection is clumped with X86.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2015-06-26 02:25:34 UTC  
> Url: https://github.com/boostorg/predef/issues/25#issuecomment-115465230  

On Thu, Jun 25, 2015 at 2:27 PM, Joel Falcou notifications@github.com  
wrote:  
  
> When dealing with SIMD instructions stes, we need to know if a CPU is an  
> actual AMD to acces SSE4A intrinsics. Currently AMD detection is clumped  
> with X86.  
>   
> I searched and could not find any way to determine specifically an AMD cpu.  
> But, there are macros defined by some compilers for the various SSE  
> variations as enabled for the particular compiler options. Hence best I  
> could do is to add some defs for those. Of course I would need to know what  
> all the various SSE compiler defs are. And it seems compiler documentation  
> on that is scarce. Though perhaps you could help with that?  
  
##   
  
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net  
-- rrivera/acm.org (msn) - grafikrobot/aim,yahoo,skype,efnet,gmail

---

## Comment 2

> Username: jfalcou  
> Created at: 2015-06-26 05:25:26 UTC  
> Url: https://github.com/boostorg/predef/issues/25#issuecomment-115520653  

The SIMD detection is a mess in itself has if GCC/CLANG actually defines macros, MSVC does not excpet for SSE2 ad AVX. I can anyway lend a hand on that yes.  
  
The other point was to have a way to know the cpu was AMD or intel to defines piece of code actually managing the SIMD part. I'll see how we can redo that.  
  
Feel free to close this issue, I'll make a PR for the SIMD detection.
