# #100 - boost::adaptors::sliced wrong document [Open]

> Username: ikedahi  
> Created at: 2019-12-27 11:15:03 UTC  
> Updated at: 2019-12-27 11:15:03 UTC  
> Url: https://github.com/boostorg/range/issues/100  

The document says:  
 Precondition: 0 <= n && n <= m && m < distance(rng)   
  
but  
m < distance(rng)  
shoud be  
m <= distance(rng)
