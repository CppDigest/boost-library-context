# #2 - BOOST_STL_INTERFACES_DOXYGEN not defined [Closed]

> Username: grattle  
> Created at: 2019-10-22 11:15:00 UTC  
> Updated at: 2019-12-29 19:54:20 UTC  
> Closed at: 2019-12-29 19:54:20 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/2  

On my system, just dragging the include files across to a local folder for compilation doesn't define BOOST_STL_INTERFACES_DOXYGEN. No idea where it might be.  
  
That causes oodles of compile errors regarding access::base.  
  
Just changing the line:  
  
#if 201703L < __cplusplus && defined(__cpp_lib_concepts) || BOOST_STL_INTERFACES_DOXYGEN  
  
to:  
  
#if 201703L < __cplusplus && defined(__cpp_lib_concepts) || defined(BOOST_STL_INTERFACES_DOXYGEN)  
  
fixes the issue for me on my system.  
  
Arthur

---

## Comment 1

> Username: grattle  
> Created at: 2019-10-22 11:50:54 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/2#issuecomment-544924930  

Oh, I'm using clang 8 on debian testing linux

---

## Comment 2

> Username: tzlaine  
> Created at: 2019-12-17 00:49:19 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/2#issuecomment-566320015  

That macro is only defined by the Doxygen pass of the doc generation.  
  
Any undefined preprocessor token is replaced with "0" in such code.  Not sure why you're seeing an issue.  Perhaps that substitution is a GNU extension.  Either way, I can easily add defined().

---

## Comment 3

> Username: hadrielk  
> Created at: 2019-12-18 03:56:41 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/2#issuecomment-566856574  

Out of curiosity is there a reason for using a macro to control doxygen? Like is it a boost requirement?  
  
I ask because it's kinda ugly to see in various places, and doxygen has supported [`cond`/`endcond`](http://www.doxygen.nl/manual/commands.html#cmdcond) in comments for many years...

---

## Comment 4

> Username: tzlaine  
> Created at: 2019-12-18 16:46:34 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/2#issuecomment-567115363  

On Tue, Dec 17, 2019 at 9:56 PM Hadriel Kaplan <notifications@github.com>  
wrote:  
  
> Out of curiosity is there a reason for using a macro to control doxygen?  
> Like is it a boost requirement?  
>  
> I ask because it's kinda ugly to see in various places, and doxygen has  
> supported cond/endcond  
> <http://www.doxygen.nl/manual/commands.html#cmdcond> in comments for many  
> years...  
>  
That does not quite work, because sometimes you want the Doxygen docs to  
show something different from what it would show without the macro.  That  
is, for documentation purposes you need not just to eliminate some code,  
but also to add in some code just for Doxygen to see.  Once you start using  
a macro for this, I feel it's best to do so consistently (rather than using  
the macro in some places and Doxygen markup in others).  
  
This is the way this is handled in other Boost libraries for this reason.
