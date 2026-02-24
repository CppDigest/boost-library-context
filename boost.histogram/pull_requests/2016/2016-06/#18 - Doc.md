# #18 Doc [Merged]

> Username: klemens-morgenstern  
> Created at: 2016-06-18 20:30:30 UTC  
> Updated at: 2016-06-20 19:34:02 UTC  
> Merged at: 2016-06-20 16:57:12 UTC  
> Closed at: 2016-06-20 16:57:12 UTC  
> Url: https://github.com/boostorg/histogram/pull/18  

Switched to quickbook: http://klemens-morgenstern.github.io/histogram/  
  
Don't know if you'll use that. This already includes the changes in the code found in #17.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-06-20 18:18:01 UTC  
> Url: https://github.com/boostorg/histogram/pull/18#discussion_r67739348  

Seems to me like it's done ;). It's actually a very minor thing (since the compiler would optimize that anyway), but you avoid a copy in std::swap.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2016-06-20 19:34:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/18#discussion_r67751784  

You are right, my bad for not reading carefully! :)  
  
On 6/20/16 2:18 PM, Klemens Morgenstern wrote:  
  
> In include/boost/histogram/detail/nstore.hpp   
> https://github.com/HDembinski/histogram/pull/18#discussion_r67739348:  
>   
> >    {  
> > -    std::swap(size_, o.size_);  
> > -    std::swap(depth_, o.depth_);  
> > -    std::swap(buffer_, o.buffer_);  
> > -   o.depth_ = d1;  
> > -   o.size_ = 0;  
> > -   o.buffer_ = static_cast<void*>(0);  
>   
> Seems to me like it's done ;). It's actually a very minor thing (since   
> the compiler would optimize that anyway), but you avoid a copy in   
> std::swap.  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/HDembinski/histogram/pull/18/files/b798f18d105070863a9ccc1d0837288f53d992e9#r67739348,   
> or mute the thread   
> https://github.com/notifications/unsubscribe/ACgnouA6CGkuZx7_4Rd5ZviPjkCnrxb9ks5qNtlagaJpZM4I5But.  
  
##   
  
Hans Dembinski  
https://github.com/HDembinski  
  
Proposed for Boost: histogram  
https://github.com/HDembinski/histogram  
  
Automatic one-dimensional unfolding of data distributions  
https://github.com/HDembinski/aru-unfolding

---
