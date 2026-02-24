# #584 - Timestamp delta calculation is erroneous [Closed]

> Username: jefftrull  
> Created at: 2020-04-28 22:06:35 UTC  
> Updated at: 2020-05-12 12:31:34 UTC  
> Closed at: 2020-05-12 12:31:34 UTC  
> Url: https://github.com/boostorg/build/issues/584  

For users interested in getting the execution time of a build step there is a `time` target feature. I have observed the third `clock` line of the output to produce negative numbers. It looks like the fault is in [this code](https://github.com/boostorg/build/blob/develop/src/engine/timestamp.cpp#L227-L230) which converts seconds to microseconds and then adds the result to a nanosecond value.  
  
For example:  
> user: [waveidl] 0.500173  
> system: [waveidl] 0.069151  
> clock: [waveidl] -429.698395  
  
Thanks @mloskot and @pdimov for helping to identify the issue.
