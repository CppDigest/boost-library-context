# #90 - boost::lower_bound takes it argument by value [Closed]

> Username: considered-useful  
> Created at: 2019-04-03 22:11:32 UTC  
> Updated at: 2020-02-13 09:38:29 UTC  
> Closed at: 2020-02-13 09:38:29 UTC  
> Url: https://github.com/boostorg/range/issues/90  

Since it was introduced in boost 1.43, `boost::lower_bound` has taken `Value` by value rather than by const ref.  I wondered if there was a reason why, seeing as `std::lower_bound` takes its value by const ref
