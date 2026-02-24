# #10 - Remove min/max macro warning or make it optional [Open]

> Username: HDembinski  
> Created at: 2019-08-22 08:59:33 UTC  
> Updated at: 2019-08-22 08:59:33 UTC  
> Url: https://github.com/boostorg/inspect/issues/10  

The max/min macro issue does not seem to be relevant anymore in modern compilers. I was completely unaware of this in Boost.Histogram and the code works fine on all msvc versions which support C++14. I added the patches to Histogram to get rid of this warning, but now my code became a lot less readable. I am considering to revert this change and propose instead to remove the min/max macro check from the inspection list.  
  
Another solution would be to blacklist checks individually for each library.
