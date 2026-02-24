# #8 - List of compiled libraries is out of date [Open]

> Username: alandefreitas  
> Created at: 2024-02-29 19:29:03 UTC  
> Updated at: 2024-04-18 12:23:29 UTC  
> Url: https://github.com/boostorg/more/issues/8  

The getting started guide says:  
  
> The only Boost libraries that must be built separately are:  
> [Boost.Chrono](https://www.boost.org/doc/libs/1_83_0/libs/chrono/index.html)  
> [Boost.Context](https://www.boost.org/doc/libs/1_83_0/libs/context/index.html)  
> [Boost.Filesystem](https://www.boost.org/doc/libs/1_83_0/libs/filesystem/index.html)  
> [Boost.GraphParallel](https://www.boost.org/doc/libs/1_83_0/libs/graph_parallel/index.html)  
> [Boost.IOStreams](https://www.boost.org/doc/libs/1_83_0/libs/iostreams/index.html)  
> [Boost.Locale](https://www.boost.org/doc/libs/1_83_0/libs/locale/index.html)  
> [Boost.Log](https://www.boost.org/doc/libs/1_83_0/libs/log/index.html) (see [build documentation](https://www.boost.org/doc/libs/1_83_0/libs/log/doc/html/log/installation/config.html))  
> [Boost.MPI](https://www.boost.org/doc/libs/1_83_0/libs/mpi/index.html)  
> [Boost.ProgramOptions](https://www.boost.org/doc/libs/1_83_0/libs/program_options/index.html)  
> [Boost.Python](https://www.boost.org/doc/libs/1_83_0/libs/python/doc/html/building.html) (see the [Boost.Python build documentation](https://www.boost.org/doc/libs/1_83_0/libs/python/doc/html/building.html) before building and installing it)  
> [Boost.Regex](https://www.boost.org/doc/libs/1_83_0/libs/regex/index.html)  
> [Boost.Serialization](https://www.boost.org/doc/libs/1_83_0/libs/serialization/index.html)  
> [Boost.Thread](https://www.boost.org/doc/libs/1_83_0/libs/thread/index.html)  
> [Boost.Timer](https://www.boost.org/doc/libs/1_83_0/libs/timer/index.html)  
> [Boost.Wave](https://www.boost.org/doc/libs/1_83_0/libs/wave/index.html)  
>   
> A few libraries have optional separately-compiled binaries:  
> [Boost.Graph](https://www.boost.org/doc/libs/1_83_0/libs/graph/index.html) also has a binary component that is only needed if you intend to [parse GraphViz files](https://www.boost.org/doc/libs/1_83_0/libs/graph/doc/read_graphviz.html).  
> [Boost.Math](https://www.boost.org/doc/libs/1_83_0/libs/math/index.html) has binary components for the TR1 and C99 cmath functions.  
> [Boost.Random](https://www.boost.org/doc/libs/1_83_0/libs/random/index.html) has a binary component which is only needed if you're using random_device.  
> [Boost.Test](https://www.boost.org/doc/libs/1_83_0/libs/test/index.html) can be used in “header-only” or “separately compiled” mode, although separate compilation is recommended for serious use.  
> [Boost.Exception](https://www.boost.org/doc/libs/1_83_0/libs/exception/index.html) provides non-intrusive implementation of exception_ptr for 32-bit _MSC_VER==1310 and _MSC_VER==1400 which requires a separately-compiled binary. This is enabled by #define BOOST_ENABLE_NON_INTRUSIVE_EXCEPTION_PTR.  
> [Boost.System](https://www.boost.org/doc/libs/1_83_0/libs/system/index.html) is header-only since Boost 1.69. A stub library is still built for compatibility, but linking to it is no longer necessary.  
  
According to `b2 --show-libraries`, the list should be:  
  
- Boost.Atomic *  
- Boost.Chrono  
- Boost.Cobalt *  
- Boost.Container *  
- Boost.Context  
- Boost.Contract *  
- Boost.Coroutine *  
- Boost.DateTime *  
- Boost.Exception *  
- Boost.Fiber *  
- Boost.Filesystem  
- Boost.Graph *  
- Boost.GraphParallel  
- Boost.IOStreams  
- Boost.JSON *  
- Boost.Locale  
- Boost.Log  
- Boost.Math *  
- Boost.MPI  
- Boost.Nowide *  
- Boost.ProgramOptions *  
- Boost.Python  
- Boost.Random *  
- Boost.Regex  
- Boost.Serialization  
- Boost.Stacktrace *  
- Boost.System *  
- Boost.Test *  
- Boost.Thread  
- Boost.Timer  
- Boost.TypeErasure *  
- Boost.URL *  
- Boost.Wave  
  
This list from `b2 --show-libraries` might include false positives but some libraries, like Boost.URL, are definitely not wrong.  
  
I would open a PR but I couldn't find the source code for this page.
