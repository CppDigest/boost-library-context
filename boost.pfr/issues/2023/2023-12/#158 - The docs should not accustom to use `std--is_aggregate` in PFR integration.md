# #158 - The docs should not accustom to use `std::is_aggregate` in PFR integration [Closed]

> Username: denzor200  
> Created at: 2023-12-26 22:26:12 UTC  
> Updated at: 2024-02-14 18:00:29 UTC  
> Closed at: 2024-02-14 18:00:28 UTC  
> Url: https://github.com/boostorg/pfr/issues/158  

https://github.com/boostorg/pfr/blob/730f40b6b2cbaec88712ecbe32c33c8ae2b24edd/doc/pfr.qbk#L164  
  
We already have `boost::pfr::is_reflectable` that's specially intended for that goal, and it's better to recomment anyone to use it instead of `std::is_aggregate`.
