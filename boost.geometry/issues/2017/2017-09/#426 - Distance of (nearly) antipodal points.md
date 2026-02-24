# #426 - Distance of (nearly) antipodal points [Closed]

> Username: vissarion  
> Created at: 2017-09-28 11:40:02 UTC  
> Updated at: 2024-10-02 10:46:57 UTC  
> Closed at: 2024-10-02 10:46:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/426  

We want to examine the behaviour of the current formulas for distance computation in geographic coordinate systems, i.e. andoyer, thomas, vincenty.   
  
We do the following experiment (on develop branch): compute the distance between (0,0) and (180,x) where x takes values from 0 to 0.00001 with step 0.0000001. Note that this is a computation along a meridian, in other words the geodesic is a meridian, that is a very special case of nearly antipodal points.  
  
Here are the distances calculated: [data.txt](https://github.com/boostorg/geometry/files/1340657/data.txt); the columns are x, andoyer, thomas and vincenty distance respectively.   
  
See below the plots of computed distance as a function of latitude x for the 3 different formulas.  
[andoyer.pdf](https://github.com/boostorg/geometry/files/1340629/andoyer.pdf)  
[thomas.pdf](https://github.com/boostorg/geometry/files/1340632/thomas.pdf)  
[vincenty.pdf](https://github.com/boostorg/geometry/files/1340633/vincenty.pdf)  
  
Vincenty seems ok for this test case; andoyer is doing a jump when x is getting close to 0; thomas seems to return more or less arbitrary numbers.   
  
In general all the above formulas are expected to return non accurate results for nearly antipodal points. This is more or less a known issue but I just put it here for future reference until we fix it.
