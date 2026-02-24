# #508 - value_to tuple/pair [Closed]

> Username: vinniefalco  
> Created at: 2021-02-28 05:45:43 UTC  
> Updated at: 2021-04-26 05:47:56 UTC  
> Closed at: 2021-04-26 05:47:56 UTC  
> Url: https://github.com/boostorg/json/issues/508  

`value_to` should work for tuple-like and pair-like types. This interacts with #466 because `array` is tuple-like and needs to be handled as a special case.
