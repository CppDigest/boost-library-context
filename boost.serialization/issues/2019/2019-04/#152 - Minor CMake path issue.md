# #152 - Minor CMake path issue [Closed]

> Username: nemothenoone  
> Created at: 2019-04-06 07:32:54 UTC  
> Updated at: 2021-06-01 19:12:30 UTC  
> Closed at: 2021-06-01 19:12:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/152  

Does the path at https://github.com/boostorg/serialization/blob/develop/CMake/CMakeLists.txt#L103 is missing an extra ```..```?  
  
I mean, if I use CMake project to build Boost.Serialization inside Boost superproject, I get ```BOOST_ROOT not found```, but extra ```..``` fixes the problem.

---

## Comment 1

> Username: robertramey  
> Created at: 2019-04-06 17:25:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/152#issuecomment-480521923  

Hmmmm ...  I've hacked my CMake implementation to support my personal debug/development of Boost serialization with my Xcode IDE.  Previously I used the mdvc IDE which was fine to do the development and debug, but hell to maintain whenever anything (boost version, msvc version, etc).  CMake was sort of pain to figure out - it's better now - but it was a one time deal.  Now whenever something changes I can just regenerate a new Xcode (and presumably MSVC or Eclipse or .. ) project and I'm back in business.  It only takes a minute.  Better I can maintain projects for different IDE simultaneously using the same source code base.  Presumable this opens the path to building the boost build type library with a single CMake command - though I've never tried this - so its unlikely to work with out some more tweaking.  There are several things that you might want to support: Building from the Cmake command line, generate correctly named boost libraries and dlls, adding support for permitting the the serialization library to be consumed as a CMake like component, supporting CDash and perhaps CMake style deployment.  As to your particular case, I'm not sure what you're actually doing so I don't know what to suggest.  All I can say is that it works for me for what I use it for.  
  
If you're using my CMake files for something different than I am you might need to tweak something.  If you find the magic enhancement - and it doesn't break it for the purposes I use it for, post it as a PR and I'll seriously consider adding it in.
