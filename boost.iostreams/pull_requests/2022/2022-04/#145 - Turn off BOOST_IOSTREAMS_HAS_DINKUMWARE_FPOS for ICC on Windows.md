# #145 Turn off BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS for ICC on Windows [Open]

> Username: malton-ont  
> Created at: 2022-04-26 14:01:18 UTC  
> Updated at: 2022-04-26 14:01:18 UTC  
> Url: https://github.com/boostorg/iostreams/pull/145  

The Intel C++ Compiler on Windows uses the MSVC runtimes, and so has the same issue with std::fpos::seekpos begin deprecated. Add BOOST_INTEL_WIN to the preprocessor checks to exclude this combination as well.

---
