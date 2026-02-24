# #15 - [feature] Exponential Smoothing [Open]

> Username: FloopCZ  
> Created at: 2018-06-15 16:47:00 UTC  
> Updated at: 2018-06-15 16:49:52 UTC  
> Url: https://github.com/boostorg/accumulators/issues/15  

Hi, is there a way to calculate simple exponential smoothing (or [exponential moving average](https://en.wikipedia.org/wiki/Moving_average#Exponential_moving_average))? That is,  
`s = a * x + (1 - a) * s` for some `0 < a < 1`  
If not, would you (or anyone) be interested in PR?
