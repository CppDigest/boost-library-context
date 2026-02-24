# #1742 - compile error C2668 with vs2017 [Closed]

> Username: zaley2000  
> Created at: 2019-10-23 06:14:12 UTC  
> Updated at: 2019-10-25 01:15:34 UTC  
> Closed at: 2019-10-25 01:15:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1742  

In "buffer_traits.cpp"  line 123, a C2668 error appear. " Ambiguous call to overloaded function net::buffer_sequence_begin ". In vs2015 there is no similar errors.  
  
using buffers_iterator_type =  
    decltype(net::buffer_sequence_begin(  
        std::declval<BufferSequence const&>()));   
  
  
BOOST 1.71  Beast 266

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-23 09:36:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1742#issuecomment-545358534  

I develop on VS2017 and I have not seen this error. Appveyor also has no trouble building it:  
https://ci.appveyor.com/project/vinniefalco/beast/history  
  
Does this error occur when you compile the tests? Can you provide a small program which reproduces the error?

---

## Comment 2

> Username: zaley2000  
> Created at: 2019-10-25 01:15:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1742#issuecomment-546160762  

When changing IDE to vs2017 (15.9), everything is OK now.
