# #168 - Add static asserts on axes type [Closed]

> Username: HDembinski  
> Created at: 2019-03-07 10:36:41 UTC  
> Updated at: 2019-04-14 20:34:40 UTC  
> Closed at: 2019-04-14 20:34:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/168  

It is documented here which axes types are supported, but we should additionally have a static assert on that.  
https://github.com/boostorg/histogram/blob/develop/include/boost/histogram/histogram.hpp#L40

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-04-14 20:34:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/168#issuecomment-483056070  

Not implemented. Users are not supposed to set the template arguments by hand, this is done correctly automatically by the `make_histogram` factory functions. The valid types are also documented. Since there is a cost in doing more run-time checks and no obvious gain, I reject this.
