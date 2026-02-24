# #87 Fixed flat_map CTAD [Merged]

> Username: tobias-loew  
> Created at: 2018-11-08 17:19:18 UTC  
> Updated at: 2018-11-12 21:58:28 UTC  
> Merged at: 2018-11-12 21:58:28 UTC  
> Closed at: 2018-11-12 21:58:28 UTC  
> Url: https://github.com/boostorg/container/pull/87  

Fixed ambiguous CTADs.  
Added new macro BOOST_HAS_CTAD_SFINAE to indicate that compiler supports SFINAE to disambiguate CTADs.   
The macro BOOST_HAS_CTAD_SFINAE is currently undefined for all compilers (new versions of gcc may support it, cf. https://github.com/boostorg/container/issues/80, but I cannot check it)

---
