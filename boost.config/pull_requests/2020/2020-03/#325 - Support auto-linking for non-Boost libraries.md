# #325 Support auto-linking for non-Boost libraries [Merged]

> Username: MarcelRaad  
> Created at: 2020-03-18 11:44:14 UTC  
> Updated at: 2020-03-24 11:11:47 UTC  
> Merged at: 2020-03-24 11:08:02 UTC  
> Closed at: 2020-03-24 11:08:02 UTC  
> Url: https://github.com/boostorg/config/pull/325  

Check for `BOOST_AUTO_LINK_NOMANGLE` before any other layouts.  
This way, `BOOST_AUTO_LINK_NOMANGLE` can be defined temporarily to link  
to non-Boost libraries even if `BOOST_AUTO_LINK_TAGGED` or  
`BOOST_AUTO_LINK_SYSTEM` are defined.  
Boost.UUID does this:  
https://github.com/boostorg/uuid/blob/24e3e0db36adfae963c9fc523d0d1e3b1f171950/include/boost/uuid/detail/random_provider_bcrypt.ipp#L20  
https://github.com/boostorg/uuid/blob/24e3e0db36adfae963c9fc523d0d1e3b1f171950/include/boost/uuid/detail/random_provider_wincrypt.ipp#L24

---
