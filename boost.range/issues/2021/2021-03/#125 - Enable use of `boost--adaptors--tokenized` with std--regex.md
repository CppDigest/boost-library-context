# #125 - Enable use of `boost::adaptors::tokenized` with std::regex [Open]

> Username: Mike-Devel  
> Created at: 2021-03-10 13:50:37 UTC  
> Updated at: 2021-03-11 13:34:23 UTC  
> Url: https://github.com/boostorg/range/issues/125  

A superficial look at https://github.com/boostorg/range/blob/develop/include/boost/range/adaptor/tokenized.hpp shows that the only reason, why there is a hardcoded dependency on boost::regex is because `regex_constants::match_default` is used as a default parameter in some of the functions. Everything else is already templateized to work with anything regex like and should work with std::regex just as well.  
  
If I get the go-ahead from you I'd like to replace those functions with two functions: One that doesn't have the parameter at all and one that takes the parameter as a tempate argument (the functions are already templated anyway). Of course I'll also have to adapt some of the internals, but all changes would be localized to that file (and the tests of course).  
  
This would have two advantages:  
  
- boost::adaptors::tokenized would work with std::regex not only with boost::regex  
- It would remove the sole dependency on Boost.Regex from Boost.Range

---

## Comment 1

> Username: Mike-Devel  
> Created at: 2021-03-11 12:40:15 UTC  
> Updated at: 2021-03-11 13:34:23 UTC  
> Url: https://github.com/boostorg/range/issues/125#issuecomment-796707455  

I had another look and I'm no lnger sure this is really possible the way I imagined it (purely "concept" based without explicitly hard-coding the supported regex libraries):   
  
I initially overlooked that tokenized.hpp also uses `regex_token_iterator` and I don't know of generic way to get from a std/boost regex to the matching `regex_token_iterator`.  
  
One could of course explicitly write down the type mapping (e.g. via partial specialization). However, I that is only possible if the regex headers are included (so eliminates advantage number 2) and is a far less generic and more error prone solution  than what I had in mind.  
  
I'm leavin this isue open for a bit, in case anyone finds an elegant solution.
