# #1064 - Document requirement of building Boost libraries for CMake on Windows [Closed]

> Username: mloskot  
> Created at: 2018-03-10 23:22:42 UTC  
> Updated at: 2018-03-12 14:47:31 UTC  
> Closed at: 2018-03-12 14:47:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1064  

The README says:  
  
> To build the documentation, examples, tests, and benchmarks it is necessary to first  
> obtain the boost "superproject" along with all of the boost libraries.  
> Instructions for doing so may be found on the Boost Wiki.   
  
The wording _obtain the boost "superproject" along with all of the boost libraries_  is insufficient in the context of using the CMake configuration on Windows which does not build any of the Boost libraries, dependencies required by Boost.Beast, unlike the Boost.Build setup.  
  
The "boost libraries" does not necessarily mean building the binary libraries.  
  
My suggestion is to complete the sentence this way:  
  
> first obtain the boost "superproject" along with sources of all of the Boost libraries, then run `b2` command to build the Boost libraries.  
  
BTW, boost should read Boost, it's a name.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-11 16:17:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1064#issuecomment-372127396  

Seems reasonable, have you considered submitting a pull request?

---

## Comment 2

> Username: mloskot  
> Created at: 2018-03-11 20:20:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1064#issuecomment-372145803  

Yes, of course, here it goes #1067
