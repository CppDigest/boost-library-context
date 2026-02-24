# #781 - Add buffers_type [Closed]

> Username: vinniefalco  
> Created at: 2017-09-15 21:50:16 UTC  
> Updated at: 2019-02-23 05:44:32 UTC  
> Closed at: 2019-02-23 05:44:32 UTC  
> Url: https://github.com/boostorg/beast/issues/781  

```  
template<class BufferSequence>  
using buffers_type = ...  
```  
  
This will be `boost::asio::const_buffer` or `boost::asio::mutable_buffer` depending on the buffer sequence. Goes in `<boost/beast/core/type_traits.hpp>`.  
  
This can replace some of the uses of `std::conditional` such as in `buffers_suffix`.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:18 UTC  
> Url: https://github.com/boostorg/beast/issues/781#issuecomment-384022525  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-05-01 18:32:34 UTC  
> Url: https://github.com/boostorg/beast/issues/781#issuecomment-385750210  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-05-31 18:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/781#issuecomment-393639544  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-02-23 05:44:32 UTC  
> Url: https://github.com/boostorg/beast/issues/781#issuecomment-466619602  

Done
