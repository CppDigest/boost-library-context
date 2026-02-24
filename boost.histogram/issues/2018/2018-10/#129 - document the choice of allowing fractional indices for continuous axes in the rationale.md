# #129 - document the choice of allowing fractional indices for continuous axes in the rationale [Closed]

> Username: HDembinski  
> Created at: 2018-10-27 19:26:01 UTC  
> Updated at: 2019-01-29 20:48:16 UTC  
> Closed at: 2019-01-29 20:48:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/129  

The library recognizes axis types that have a `value` method which accepts a float. Any such axis is assumed to represent a contiguous range of values, e.g. regular, circular, ...  
  
Fractional indices are a simple and efficient way to distinguish these from axis that represent discrete or discontinuous bins, whose `value` method would accept an integer instead. With a fractional index, computing the bin center is very efficient.
