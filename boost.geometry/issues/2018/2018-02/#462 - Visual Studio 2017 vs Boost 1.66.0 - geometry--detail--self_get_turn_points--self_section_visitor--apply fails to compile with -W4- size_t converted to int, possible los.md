# #462 - Visual Studio 2017 vs Boost 1.66.0 - geometry::detail::self_get_turn_points::self_section_visitor::apply fails to compile with /W4: size_t converted to int, possible loss of data, warning converted to error. [Closed]

> Username: ncook-hxgn  
> Created at: 2018-02-27 14:47:35 UTC  
> Updated at: 2018-02-28 09:00:58 UTC  
> Closed at: 2018-02-27 19:49:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/462  

Hi,  
  
We're looking at upgrading our compiler to VC141 and boost is one of our dependencies.   
I saw that 1.66.0 came out too close to the release of VS 2017 for you guys to be happy with your testing, so I've been tentatively looking at upgrading boost so that it can be built with VC141 along with the rest of our software. I found a few things, but nothing huge - we compile with /W4, I've been using Visual Studio 2017 Professional v15.5.6.  
  
1) In \boost_1_66_0\boost\geometry\algorithms\detail\overlay\self_turn_points.hpp,   
This can be fixed by inserting static_casts at line 113 and line 114 around the m_ arguments, which are size_t. Not sure if right fix but easy one for me.  
Warning C4267: 'argument': conversion from 'size_t' to 'int', possible loss of data (compiling source file ...) ... D:\Development\...\3rdParty\boost\boost\geometry\algorithms\detail\overlay\self_turn_points.hpp	118	  
  
2) In \boost_1_66_0\boost\range\concepts.hpp at line 255, there is a prefix to a class member declaration which causes the /W4 compilation of boost 1.66.0 to fail with the following error message:  
  
Error C4596: 'difference_type': illegal qualified name in member declaration (compiling source file ...)	... D:\Development\.....\3rdParty\boost\boost\range\concepts.hpp	255	  
 - fix was simply deleting 'RandomAccessIteratorConcept::'  
  
"..." where I've deleted something.  
  
I hope this information is useful, let me know if I can be of more help.

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-02-27 19:48:53 UTC  
> Updated at: 2018-02-27 19:50:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/462#issuecomment-369003465  

Hi,  
  
Thanks for the report.  
  
The first issue was already fixed in develop (see: https://github.com/boostorg/geometry/commit/30bcbf2ced56c1516e73882f583aa2859454f6c7) and will be released with Boost 1.67.   
  
The second issue is in Boost.Range and should be posted there (https://github.com/boostorg/range). You could consider creating a pull request which should make fixing it faster. Assuming this is a bug in Boost.Range and not in VS 2017.

---

## Comment 2

> Username: ncook-hxgn  
> Created at: 2018-02-28 09:00:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/462#issuecomment-369166716  

Cheers @awulkiew, and thanks for the direction. Glad to hear one of these is fixed already :)  
I'll head over to Boost.Range and see if I can figure out how to do a pull request.
