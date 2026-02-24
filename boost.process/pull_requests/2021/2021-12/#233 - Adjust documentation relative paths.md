# #233 Adjust documentation relative paths [Merged]

> Username: alandefreitas  
> Created at: 2021-12-28 22:54:46 UTC  
> Updated at: 2021-12-31 13:50:07 UTC  
> Merged at: 2021-12-31 13:50:06 UTC  
> Closed at: 2021-12-31 13:50:06 UTC  
> Url: https://github.com/boostorg/process/pull/233  

The current Jamfile is counting on the git layout instead of the boost release layout to generate the documentation. We can check the links are now correct for the release layout with `b2 libs/process/doc` from the boost root directory. This PR adjusts the documentation Jamfile with a `path-constant` for the doxygen command.   
  
This is circumstantially not very problematic at many places in the documentation because the b2 glob command interprets paths relative to the Jamfile (see https://github.com/boostorg/utility/pull/86#issuecomment-989294010), but that's not the case for all commands in there. For instance, the links on the headers like "Header <boost/process/child.hpp>" in the current [reference page](https://www.boost.org/doc/libs/1_78_0/doc/html/process/reference.html) are broken. This PR should probably fix this problem.

---
