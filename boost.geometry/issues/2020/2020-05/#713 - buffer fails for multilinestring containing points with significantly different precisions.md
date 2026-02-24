# #713 - buffer fails for multilinestring containing points with significantly different precisions [Closed]

> Username: patrickop  
> Created at: 2020-05-16 20:13:33 UTC  
> Updated at: 2021-05-12 09:12:26 UTC  
> Closed at: 2021-05-12 09:12:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/713  

The below `multilinestring` contains two `linestrings`, one is 1 unit long, the other 1e-7 units.  
Now, when this `multilinestring` is `buffer`ed, the resulting `multipolygon` is empty.  
If the `multilinestring` only contains either one of the two `linestrings`, the `buffer` algorithm works as I would expect.  
My expectation when `buffer`ing a `multilinestring` is that the resulting `multipolygon` is a union of the results of applying the `buffer` algorithm on each of the constituent `linestrings`.  
Here is an example of the bug  
https://wandbox.org/permlink/c9qa6lYG0mMR5hwn  
Here is the offending `multilinestring`:  
`MULTILINESTRING((1 1,1 0),(1e-7 0,0 0))`

---

## Comment 1

> Username: tinko92  
> Created at: 2020-05-16 21:31:54 UTC  
> Updated at: 2020-05-17 09:08:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/713#issuecomment-629708499  

While this is neither a real explanation nor a general solution, if I add the #define for BOOST_GEOMETRY_NO_ROBUSTNESS before including Boost.Geometry and thereby disable rescaling (you could also set it via g++ by adding -DBOOST_GEOMETRY_NO_ROBUSTNESS), I get an output that looks like the union (if I didn't misread it) of the output for the individual linestrings. And according to the source comments, "no rescaling" will eventually become the default. I hope this helps with what you're trying to do.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-05-12 09:12:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/713#issuecomment-839607713  

Sorry for the long delay.  
Agree with @tinko92 , I hope BOOST_GEOMETRY_NO_ROBUSTNESS is the default soon and I will close this issue.  
  
https://godbolt.org/z/7zjW5Wz4a
