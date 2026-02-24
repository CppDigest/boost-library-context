# #366 - Support for position-independent code generation (linking static boost libraries into shared libraries with -fPIC) [Open]

> Username: jeking3  
> Created at: 2018-11-14 13:05:42 UTC  
> Updated at: 2021-06-11 01:56:01 UTC  
> Url: https://github.com/boostorg/build/issues/366  

From a discussion in Boost.Serialization and on the mailing list, it was understood that the gcc compiler jam enables `-fPIC` if linking shared.  Another use case is linking static, but then incorporating that into a shared library.  On some platform/compiler combinations this requires special treatment, for example with linux and gcc or clang, `-fPIC` is required to enable this to work.  
  
From the mailing list I received advice on testing for -fPIC support in the compiler and then enabling it on a given project.  @pdimov suggested that to do it properly would require some work in Boost.Build, but I think it's worth the effort.  
  
> Steven Watanabe wrote:  
> ...  
>   
> > import flags ;  
> > project  
> >  : requirements  
> >    [ check-has-flag <cxxflags>-fPIC : <cxxflags>-fPIC ]  
> >  ;  
> >  
> > Note 1: The documentation for this is only in  
> > `b2 --help flags.check-has-flag`.  It isn't  
> > integrated into the html docs yet.  
> >  
> > Note 2: This is pretty new, so if you run into  
> > any problems, please let me know.  
> >  
> > Note 3: It might be more reliable to separate  
> > the object files out and set <link>shared on them:  
> >  obj x.o : x.cpp : <link>shared ;  
> >  ...  
> >  lib l : x.o y.o ... ;  
> > (This will only work if the library is one  
> > that is specifically built for the test, not  
> > if it's a generic Boost library.)  
>   
> Note 4: (from @pdimov)  
>   
> **This should probably be a feature, like `<position-independent>on`, which   
> toolsets translate to -fPIC as appropriate.**  
>

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:22 UTC  
> Url: https://github.com/boostorg/build/issues/366#issuecomment-859203503  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
