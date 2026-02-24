# #236 - Boost Config check for std::optional [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 18:56:30 UTC  
> Updated at: 2018-08-04 18:21:37 UTC  
> Closed at: 2018-08-04 18:21:37 UTC  
> Url: https://github.com/boostorg/config/issues/236  

Please can you add a check for the availability of C++17's std::optional. I think this would be really helpful because:  
  
    AFAIA, the major compilers all now have optional in std:: (rather than in std::experimental::)  
    I get the impression that people are fast adopting std::optional as a standard part of their type vocabulary.  
    I know at least one other ticket (ticket:12175) that's declining to support std::optional until Boost Config offers this feature test.   
  
I'm not sure about Boost Config's policy about how it interacts with the standard's ​feature testing macros (which include one for std::optional). If you expect other libraries to use those feature tests directly, please can you point me to documentation for that policy that I can we can point other library devs to?  
  
Thanks very much.  
  
Moved from Trac.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-04 17:49:12 UTC  
> Url: https://github.com/boostorg/config/issues/236#issuecomment-410466317  

Other ticket referenced: https://svn.boost.org/trac10/ticket/12175
