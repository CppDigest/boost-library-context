# #13 - [feature request] add serialize/ deserialize functionality [Closed]

> Username: yuvalif  
> Created at: 2017-12-21 08:17:27 UTC  
> Updated at: 2019-05-14 19:05:01 UTC  
> Closed at: 2019-05-14 19:05:01 UTC  
> Url: https://github.com/boostorg/accumulators/issues/13  

This was also discussed here: https://stackoverflow.com/questions/32289719/how-to-serialize-boostaccumulatorsaccumulator-set  
  
The ability to store the internal state of an object, as well as to initialize it with state is would be very useful, as this would eliminate the need to store the actual data point.  
Currently the different classes cannot be extended by inheritance, since their internal state is private.  
Anyway, since serialization and deserialization does not have any size/speed impact on the class, it is probably better to add this to the class and not to inherited version of it.  
Probably good to use the boost serialization library, to assure that the output is portable (serialize on one machine and deserialize on another).
