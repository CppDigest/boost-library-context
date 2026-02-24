# #75 SSL input buffer [Closed]

> Username: arvidn  
> Created at: 2017-12-14 10:28:28 UTC  
> Updated at: 2017-12-14 11:16:06 UTC  
> Closed at: 2017-12-14 11:16:06 UTC  
> Url: https://github.com/boostorg/asio/pull/75  

Fix for https://github.com/boostorg/asio/issues/74  
  
This makes the ``ssl::stream`` call ``read_some()`` on its underlying socket type with a *MutableBufferSequence* rather than just a ``mutable_buffer``.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-14 10:42:01 UTC  
> Url: https://github.com/boostorg/asio/pull/75#issuecomment-351674308  

Sorry for the breakage, but this is a side effect of an intended change. The buffer sequence requirements have been modified; `const_buffer` and `mutable_buffer` are now considered buffer sequences. Please use `buffer_sequence_begin` and `buffer_sequence_end` to obtain a begin and end iterator, respectively.  
  
New [MutableBufferSequence](http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/MutableBufferSequence.html) requirements.

---

## Comment 2

> Username: arvidn  
> Created_at: 2017-12-14 11:08:32 UTC  
> Url: https://github.com/boostorg/asio/pull/75#issuecomment-351680644  

thanks!

---
