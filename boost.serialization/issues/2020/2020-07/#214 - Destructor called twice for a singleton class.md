# #214 - Destructor called twice for a singleton class [Open]

> Username: shivarajshivu  
> Created at: 2020-07-29 22:31:42 UTC  
> Updated at: 2020-07-30 03:16:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/214  

Hi,  
  
I'm facing an issue. I have an executable and a shared library. I have one common singleton class which is used by both by executable and also the shared library.  
  
When the library unloads it's calling the destructor and when the executable is terminating it's also calling the destructor due to which program is crashed at termination due to double free corruption.   
  
How to handle static variables when its shared b/w an executable and a shared library.  
  
Can you please assist me in resolving this issue.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-07-30 03:16:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/214#issuecomment-666066959  

The way "singleton" works is that it creates an instance for each executable module.  Normally this is just the *.exe file.  But when one has a *.dll/shared library, it creates one for each shared library as well.  This is a "feature" of static variables in C/C++.  Normally this works OK if (and only if) each all the serialization for a give type is confined within one shared library.  In this case one can load/unload shared libraries and the appropriate static variables will be unloaded/destroyed.  Problems can arise when the serialization for a give type is used from within two modules.  It seems the confusion reigns then.  If you look at the tests for dll/shared libraries you can see the extra efforts I made to avoid serialization code for a given type being implemented in more than one module.  For example, for types whose serialization is defined as part of a shared library, I don't use a header implementation as that would allow the code to be generated from the place where it's called rather than from the shared library where other code is defined.  
  
Of course the real fun begins when the base class is defined on one module (typically main) and serialization for a derived type is defined within a shared library.  
  
I hope that makes sense. I believe it's addressable, but one has to understand not just the C++ language but how it is implemented.  Note that the standard itself makes no reference to dynamic linking (or static linking for that matter).  So we're stuck figuring out.  
  
Again take a look at the tests for shared libraries.
