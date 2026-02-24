# #6 - p_square_quantile incorrect for few observations [Open]

> Username: breese  
> Created at: 2016-10-23 15:28:59 UTC  
> Updated at: 2016-10-23 15:28:59 UTC  
> Url: https://github.com/boostorg/accumulators/issues/6  

The P^2 algorithms assumes that 5 observations are available from the beginning.  
  
The `p_square_quantile` class make the same assumption, and it always returns the middle height (index 2) even when there are less than 6 observations.  
  
A special case is needed for count < 6.  
  
A solution is to always sort the heights when count < 6. Then we can return something like this (where `cnt` is the current count)  
  
```  
result_type result(dont_care) const  
{  
    if (cnt >= 6)  
    {  
        return this->height[2];  
    }  
    else  
    {  
        const std::size_t nearest_rank = std::ceil(cnt * p);  
        return this->height[nearest_rank - 1];  
    }  
}  
```
