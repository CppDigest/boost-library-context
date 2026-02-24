# #10 Unused typedef in norm causes compiler warning [Merged]

> Username: g4wjs  
> Created at: 2017-02-28 21:49:14 UTC  
> Updated at: 2017-02-28 23:29:35 UTC  
> Merged at: 2017-02-28 22:10:20 UTC  
> Closed at: 2017-02-28 22:10:20 UTC  
> Url: https://github.com/boostorg/interval/pull/10  

The unused typedef in the body of norm() causes:  
  
/boost/boost/numeric/interval/utility.hpp: In function 'T boost::numeric::norm(const boost::numeric::interval<T, Policies>&)':  
/boost/boost/numeric/interval/utility.hpp:251:33: warning: typedef 'I' locally defined but not used [-Wunused-local-typedefs]  
   typedef interval<T, Policies> I;  
                                 ^

---
