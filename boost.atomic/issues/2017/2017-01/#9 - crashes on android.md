# #9 - crashes on android [Closed]

> Username: joycepg  
> Created at: 2017-01-18 00:22:43 UTC  
> Updated at: 2017-01-18 02:53:45 UTC  
> Closed at: 2017-01-18 02:53:45 UTC  
> Url: https://github.com/boostorg/atomic/issues/9  

we are experiencing issues that seem to be traceable to boost atomic on android (specifically Nexus 7 2013). see [libtorrent issue](https://github.com/arvidn/libtorrent/issues/1225)  
could you please advise us on the correct compile options for this platform?  
  
we are trying the default settings (no special flags), and also `-DBOOST_ASIO_DISABLE_STD_ATOMIC=1 -DBOOST_SP_NO_ATOMIC_ACCESS=1`, but still see the issue.  
  
we are not trying to build boost.thread; libtorrent just uses it as a header library. is that correct? when would you need to build boost.thread?

---

## Comment 1

> Username: timblechmann  
> Created at: 2017-01-18 02:53:45 UTC  
> Url: https://github.com/boostorg/atomic/issues/9#issuecomment-273368685  

the smart pointers do not use boost.atomic
