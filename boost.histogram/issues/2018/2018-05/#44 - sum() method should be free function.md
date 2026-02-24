# #44 - sum() method should be free function [Closed]

> Username: HDembinski  
> Created at: 2018-05-02 07:59:31 UTC  
> Updated at: 2018-07-04 23:19:56 UTC  
> Closed at: 2018-07-04 23:19:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/44  

Methods should not do possibly time-consuming computations, since they model attribute access rather than transformation.
