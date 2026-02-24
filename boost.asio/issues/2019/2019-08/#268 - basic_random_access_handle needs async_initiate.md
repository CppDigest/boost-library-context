# #268 - basic_random_access_handle needs async_initiate [Closed]

> Username: cedral  
> Created at: 2019-08-12 16:38:12 UTC  
> Updated at: 2020-12-30 01:08:07 UTC  
> Closed at: 2020-12-30 01:08:06 UTC  
> Url: https://github.com/boostorg/asio/issues/268  

I am using it with use_awaitable and I had to alter it manually to use the new async_iniate model. It's an easy change but I would prefer not to have to remember to do that every time I download a new version of boost.   
  
I looked into creating a pull request for it but the development model for this library is making that a little opaque. This location appears to be auto generated from the other non boost location and yet there are changes here that don't seem to exist in that location. Also the changes here appear to incorporate pull requests with the committer changed so even if this is the right place I am not sure what the point would be.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:08:05 UTC  
> Url: https://github.com/boostorg/asio/issues/268#issuecomment-752292718  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#710](https://github.com/chriskohlhoff/asio/issues/710).
