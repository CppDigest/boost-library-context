# #292 - Boost.Context support for emscripten [Open]

> Username: Retro52  
> Created at: 2025-02-19 16:02:33 UTC  
> Updated at: 2025-02-19 16:02:33 UTC  
> Url: https://github.com/boostorg/context/issues/292  

Does Boost.Context support Emscripten? I was trying to add emscripten support to the application I am working on. In the process, I also decided to update the older version of Boost that we use (1.7X) to the latest version (main branch, in-development 1.88.0), to check if one of the issues that I have was somehow fixed in the latest versions.  
I did not find any explicit information, on whether Boost supports the Emscripten platform, but a quick search through sources found plenty of places with conditional compilation for the platform, so I assumed that it is supported at least to some degree.  
Now, when I tried to compile the program, I got a bunch of errors from the context library, specifically errors related to assembly files (which are not supported).  
Is it something I did wrong when updating the library, or is Emscripten unsupported by some of the boost libraries?
