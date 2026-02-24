# #185 Added boost::optional test case to X3 alternative parser from Qi [Merged]

> Username: vtnerd  
> Created at: 2016-04-12 17:41:02 UTC  
> Updated at: 2016-04-12 20:54:32 UTC  
> Merged at: 2016-04-12 20:54:32 UTC  
> Closed at: 2016-04-12 20:54:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/185  

X3 already works when given a `boost::optional` attribute, although I did not see any test cases for this in X3. I think the behavior in Qi and X3 are now similar - only the expected attribute differed between the versions.  
  
I did not implement the sequence flattening with the alternative parser, as Qi does not do this! Will post to mailing list shortly.

---
