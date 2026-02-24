# #360 - Static linking of boost-thread in MFC-DLL and __pRawDllMain [Open]

> Username: jdx-john  
> Created at: 2021-10-21 12:13:50 UTC  
> Updated at: 2021-10-21 12:13:50 UTC  
> Url: https://github.com/boostorg/thread/issues/360  

When we started using vcpkg we found a problem similar to this: https://stackoverflow.com/questions/3466944/upgrade-of-boost-1-35-to-1-43-causes-linker-error-with-prawdllmain-mfc-relate  
There is a workaround here: https://svn.boost.org/trac10/ticket/8550  
  
In our case however, we only see errors at run-time, not build-time. So more regressions could be lurking - the issue is boost-thread gets auto-linked by vcpkg _even in projects that are not using it_.  
  
Another workaround is to change the linkage order but we don't explicitly set boost-thread as a linker dependency and it seems really crude to kludge it in.  
  
So I am asking if anything has changed since 1.52 / 2011 how this can best be addressed?
