# #931 - Explore if normalization functions should be noexcept [Open]

> Username: alandefreitas  
> Created at: 2025-10-13 21:31:20 UTC  
> Updated at: 2026-01-23 05:39:10 UTC  
> Url: https://github.com/boostorg/url/issues/931  

In https://github.com/boostorg/url/issues/929, we discuss how we should improve the documentation of the normalization functions.  
  
A related question is whether the property that normalization never makes the string larger is stable, and whether this could be included as part of the contract. If this is very stable, these functions could be noexcept.   
  
Other applications could also benefit from that guarantee, as indicated by Mohammad Nejati, who's working on Boost.HTTP. On the other hand,   
Peter Dimov made the point that basing the noexcept-ness on an incidental property doesn't seem correct. I see three cases where this could happen:  
  
- We missed some details when implementing the RFC  
- Some other RFC obsoletes the current normalization algorithm for URLs  
- The library can accept some typically scheme-specific characters, and that means normalization can lead to longer strings (missing trailing slashes, 1-byte decoded characters that should be percent encoded as three chars, default path filenames, default ports, IDNs).  
  
If any of these conditions happen, we would have to either revert the contract or create "`normalize_v2`" functions.
