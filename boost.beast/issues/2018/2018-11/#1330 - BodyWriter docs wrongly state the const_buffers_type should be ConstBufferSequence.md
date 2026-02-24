# #1330 - BodyWriter docs wrongly state the const_buffers_type should be ConstBufferSequence [Closed]

> Username: JCYang  
> Created at: 2018-11-30 07:44:08 UTC  
> Updated at: 2019-01-08 00:27:42 UTC  
> Closed at: 2019-01-08 00:27:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1330  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
1.68.0  
  
### Steps necessary to reproduce the problem  
  
No,  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/concepts/BodyWriter.html  possibly wrong docs? W::const_buffers_type is said to be expected as ConstBufferSequence, but what the codes really expect is something like boost::asio::const_buffer. const_buffer and ConstBufferSequence are two things. whether I am correct or wrong?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-30 14:34:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1330#issuecomment-443221480  

The docs are correct, can you please provide more information (such as a small code sample)? I have no idea what problem you are encountering.

---

## Comment 2

> Username: JCYang  
> Created at: 2018-12-01 07:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1330#issuecomment-443406231  

just check out the mostly referred basic_file_body implementation example, the W::const_buffers_type is boost::asio::const_buffer. it is 'one' buffer, not a buffer-sequence.   
ConstBufferSequence require to support boost::asio::buffer_sequence_begin() and boost::asio::buffer_sequence_end().  
Your BodyReader docs is correct on the arguments type for reader.put(), where it really expect a ConstBufferSequence.  
I've written my own custom body that also utilize boost::asio::const_buffer as the W::const_buffers_type, the result is,  it works.  
So this probably concludes that the doc of BodyWriter on this very subject is wrong.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-01 09:05:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1330#issuecomment-443411816  

Both `boost::asio::const_buffer` and `boost::asio::mutable_buffer` are buffer sequences. See:  
https://github.com/boostorg/asio/blob/5ac54042c99d4f1595d4041b00b9b28752eda16e/include/boost/asio/buffer.hpp#L385  
https://github.com/boostorg/asio/blob/5ac54042c99d4f1595d4041b00b9b28752eda16e/include/boost/asio/buffer.hpp#L391  
https://github.com/boostorg/asio/blob/5ac54042c99d4f1595d4041b00b9b28752eda16e/include/boost/asio/buffer.hpp#L438  
https://github.com/boostorg/asio/blob/5ac54042c99d4f1595d4041b00b9b28752eda16e/include/boost/asio/buffer.hpp#L444  
  
I agree this is kind of confusing but this is what happens when features are designed by committee (WG21 in this case).

---

## Comment 4

> Username: JCYang  
> Created at: 2018-12-01 13:30:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1330#issuecomment-443426375  

oh... It's really really confusing, because  
  
> An iterator type meeting the requirements for bidirectional iterators (C++Std [bidirectional.iterators]) whose value type is convertible to const_buffer.   
  
 which make me think it(const_buffer) is indeed just a buffer(as the name suggested, it should be a buffer atom...), and ConstBufferSequence is a container for a list of "const_buffer"... bad wording... at least  
  
thanks for the clarify.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-12-01 17:54:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1330#issuecomment-443444835  

If `cb` is of type `boost::asio::const_buffer`, then the expression `boost::asio::buffer_sequence_begin(cb)` returns a value of type `boost::asio::const_buffer const*` (a pointer). If you think about it, a pointer meets all those requirements. It is bidirectional iterator, whose value_type is `const_buffer`.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-12-31 18:21:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1330#issuecomment-450677210  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2019-01-08 00:27:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1330#issuecomment-452131207  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
