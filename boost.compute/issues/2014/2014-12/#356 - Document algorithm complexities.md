# #356 - Document algorithm complexities [Open]

> Username: kylelutz  
> Created at: 2014-12-29 17:45:50 UTC  
> Updated at: 2017-03-21 04:14:53 UTC  
> Url: https://github.com/boostorg/compute/issues/356  

Add more documentation for the computational complexities, memory requirements, and extent of parallelization for all of the Boost.Compute algorithms.  
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-06-20 08:58:23 UTC  
> Url: https://github.com/boostorg/compute/issues/356#issuecomment-113734097  

Do you have any idea how it should look like?   
  
Algorithms have at least two versions depending on input size, input type and used device, and often these versions have versions based on supported OpenCL version, etc. (By "version" I mean different computational complexity, memory requirements, and extent of parallelization.)   
  
Should every version be documented or maybe the worst and the best scenarios?  
  
I'm asking because I can start adding such a documentation for algorithms I will do or modify.
