# #10 - add more detail on the variance estimates [Closed]

> Username: HDembinski  
> Created at: 2016-05-05 22:18:13 UTC  
> Updated at: 2017-04-25 07:36:39 UTC  
> Closed at: 2017-04-25 07:36:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/10  

> I've read that you computes variance: can that computation be  
> switched-on/off (e.g. I might not need it)? Also, there are various online  
> (single pass, weighted) variance algorithms: some a stable, other not.  
> Which one have you implemented? Does is use std::accumulate? It would be  
> nice to reassure numerically focused users about the level of quality of he  
> internals.  
> I am not using std::accumulate, since it does not fit into my scheme.  
> If you do not fill the histogram with weighted events, there is no overhead involved for the variance estimate.  
  
If you fill the histogram with normal data, without using weights, then the variance computation is done on-the-fly when the user requests it via histogram::variance(...). When no are weights involved, the variance estimate per bin is equal to the count in that bin (a common estimate based on Poisson theory). When weights were used during the filling, the variance estimate is the sum of squared weights. Storing the sum of squared weights for each bin requires twice the memory.  
  
I did not implement an option to switch that off, since in statistical analysis, the variance estimate is as important as the actual count. If you have a special case with weighted data, I think it is safe to assume that a variance estimate is also desired.  
  
I will put in more details on these things into the Notes section of the documentation.
