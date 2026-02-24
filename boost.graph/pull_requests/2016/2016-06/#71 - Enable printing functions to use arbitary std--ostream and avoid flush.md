# #71 Enable printing functions to use arbitary std::ostream and avoid flush [Merged]

> Username: e-kwsm  
> Created at: 2016-06-20 00:34:36 UTC  
> Updated at: 2016-10-31 20:10:45 UTC  
> Merged at: 2016-10-31 20:10:45 UTC  
> Closed at: 2016-10-31 20:10:45 UTC  
> Url: https://github.com/boostorg/graph/pull/71  

Printing functions in `boost/graph/graph_utility.hpp` currently print their contents to standard output (`std::cout`); they should accept `std::ostream`, such as `std::cerr` and `std::ofstream`.  
The default argument for `std::ostream` is `std::cout`, so I believe this is backward-compatible.  
Additionally I replace `std::endl` with `'\n'` to avoid flush.

---
