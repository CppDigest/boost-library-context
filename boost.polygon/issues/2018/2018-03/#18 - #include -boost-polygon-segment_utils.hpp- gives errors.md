# #18 - #include <boost/polygon/segment_utils.hpp> gives errors [Open]

> Username: jwakely  
> Created at: 2018-03-22 12:32:56 UTC  
> Updated at: 2018-03-22 12:32:56 UTC  
> Url: https://github.com/boostorg/polygon/issues/18  

This header doesn't seem to be valid on its own, it only works if included after `<boost/polygon/polygon.hpp>` has been included.  
  
Either this should be documented very prominently (it doesn't seem to be) or the header should be self-sufficient and include `<boost/polygon/polygon.hpp>` itself, or give a clear `#error` explaining that the other header needs to be included first.
