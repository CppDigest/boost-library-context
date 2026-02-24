# #411 - Cannot find <process/v2/src.hpp> [Closed]

> Username: dmenendez-gruposantander  
> Created at: 2024-10-16 10:54:15 UTC  
> Updated at: 2025-02-26 15:00:58 UTC  
> Closed at: 2025-02-26 15:00:58 UTC  
> Url: https://github.com/boostorg/process/issues/411  

The docs [here](https://www.boost.org/doc/libs/master/doc/html/boost_process/v2.html#boost_process.v2.introduction.src) say: "Boost.process v2 supports separate compilation similar to other boost libraries. It can be achieved by defining BOOST_PROCESS_V2_SEPARATE_COMPILATION and including <process/v2/src.hpp> in a single compile unit."  
  
But that file `src.hpp` is nowhere to be found, neither in the official release nor the github repository.  
  
Is this a typo in the docs?  
If not, where can I find that file?  
  
Thanks in advance for the help!

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-10-18 07:54:55 UTC  
> Url: https://github.com/boostorg/process/issues/411#issuecomment-2421727970  

I forgot to update the docs for the last release. As of boost 1.86 v2 is a compiled library. So you'll need to link it, and the src.hpp is not available anymore.

---

## Comment 2

> Username: dmenendez-gruposantander  
> Created at: 2024-10-18 08:00:00 UTC  
> Url: https://github.com/boostorg/process/issues/411#issuecomment-2421748441  

Thanks for the info!  
I would suggest to update the docs to avoid future confusions.
