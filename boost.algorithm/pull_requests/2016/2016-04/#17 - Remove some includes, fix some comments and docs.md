# #17 Remove some includes, fix some comments and docs [Merged]

> Username: kundor  
> Created at: 2016-04-29 20:24:26 UTC  
> Updated at: 2016-04-29 23:07:27 UTC  
> Merged at: 2016-04-29 23:07:27 UTC  
> Closed at: 2016-04-29 23:07:27 UTC  
> Url: https://github.com/boostorg/algorithm/pull/17  

As a result of commit 4dac507 (Remove code to use standard library versions of algorithms), most of the files in cxx11 were including `<algorithm>` unnecessarily.  
  
I suggest removing those includes where they are not used, and also removing comments which misleadingly said "We will use the standard one if it is available, otherwise we have our own implementation."  
  
Also, the file copy_if.hpp wasn't including `<utility>` where pair and make_pair can be found, and partition_point was missing an `#include <iterator>`.  
  
A second commit removes similar unused includes in cxx14/is_permutation.hpp and cxx14/mismatch.hpp, and adds a missing `#include <iterator>` to cxx14/equal.hpp.  
  
A third commit removes incorrect statements from the docs.

---
