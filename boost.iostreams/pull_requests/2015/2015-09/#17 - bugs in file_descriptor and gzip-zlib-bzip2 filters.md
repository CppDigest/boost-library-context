# #17 bugs in file_descriptor and gzip-zlib-bzip2 filters [Merged]

> Username: yanikibo  
> Created at: 2015-09-14 12:27:42 UTC  
> Updated at: 2016-12-06 19:29:18 UTC  
> Merged at: 2016-12-05 20:01:07 UTC  
> Closed at: 2016-12-05 20:01:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17  

zlib: recover Z_BUF_ERROR conditions  
file_descriptor: openmode compatibility to C++ standard

---

## Review 1 [Commented]

> Username: eldiener  
> Created_at: 2016-12-04 12:01:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/17#pullrequestreview-11293327  

📁 include/boost/iostreams/filter/zlib.hpp

```diff
 349 |     zlib_error::check BOOST_PREVENT_MACRO_SUBSTITUTION(result);
 350 |-     return result != zlib::stream_end; 
 350 |+     return result == zlib::okay;
```

> Username: eldiener  
> Created_at: 2016-12-04 12:01:31 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90775905  

According to the zlib docs, if zlib::finish is passed  in the call to deflate then zlib::stream_end is returned when successful, otherwise zlib::okay is returned when successful. I do not think your change reflects that reality.

> Username: yanikibo  
> Created_at: 2016-12-04 17:04:27 UTC  
> Updated_at: 2016-12-04 18:34:17 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90781135  

It's for handling Z_BUF_ERROR condition.  
If you analyze the code carrefully, you can see that the return value of the filter function is used to flush the buffer, not to check the stream_end.  
If we treat Z_BUF_ERROR as an error and throw an exception, then (result != zlib::stream_end) == (result == zlib::okay), because the result may only have two values (okay or stream_end). So in that case the patch is ok.  
If we do not treat Z_BUF_ERROR as an error, then we should return false to flush the buffer.  
In that case:  
    **symmetric_filter::write** flushes the buffer according to the return value of this function, and continue until the real end of stream is reached.  
    **symmetric_filter::read** returns by the size of the data read, not by EOF(-1). Thus the user will be able to call this function again by providing a buffer big enough.

> Username: eldiener  
> Created_at: 2016-12-05 00:15:27 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90790816  

Looking at the explanation for 'filter' in boost/iostreams/filter/symmetric.hpp it does not seem that a return of Z_BUF_ERROR from calling xdeflate can be handled properly. Since we return false in that case, rather than throwing an exception as previously, doesn't that mean that iostreams treats the operations as completing successfully where it really needs to be called again. At least throwing the exception alerts the end-user to the buffer problem whereas with your change any buffer problem is masked by your handling of the Z_BUF_ERROR condition.

> Username: yanikibo  
> Created_at: 2016-12-05 09:20:48 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90825821  

It's not always a user-buffer error, conversely it's an internal buffer error usually. You can get this error with a very big user buffer. It's meaningless to throw an exception for an internal recoverable error. I use this library intensively for io operations with high throughput, and this fix really works for Z_BUF_ERROR conditions.

> Username: yanikibo  
> Created_at: 2016-12-05 09:31:16 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90827477  

_Since we return false in that case, rather than throwing an exception as previously, doesn't that mean that iostreams treats the operations as completing successfully where it really needs to be called again._  
No it does not. Because the filter function does not guarantee to complete the job in a single call, instead the outer functions check the end of stream and continue to call this function if it is not reached. Also user should know that the read and write functions does not guarantee to complete the job in a single call due to buffer issues. User should always check the completeness. So that it's meaningless to throw an exception for a situation already have to be known.

> Username: yanikibo  
> Created_at: 2016-12-05 09:41:13 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90829132  

The read and write functions of iostreams classes is designed to return the size of the data operated. It informs the user by an extra call returning -1 after the operation is completed.

> Username: eldiener  
> Created_at: 2016-12-05 12:40:20 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90856446  

OK, I agree with your analysis. I would still like to see, in place of `result == zlib::okay` the specific test for end-of-stream and buffer error in the form of `result != zlib::stream_end && result != zlib::buf_error` instead. The same goes for your second case below. I am being as conservative as I can be in this change so as to invoke only the particular consequences by which a Z_BUF_ERROR is now handled differently. In other words currently not reaching the end-of-stream returns 'true', and any other result returns 'false' while with your fix I want to have it explicitly changed so that not reaching the end-of-stream and not having a buffer error returns 'true', and any other result returns 'false'.

> Username: yanikibo  
> Created_at: 2016-12-05 18:36:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90927205  

The right way is to return (result == zlib::okay) because when we return true, the symmetric_filter continues filtering. However if there is an error (not throwing exception), it should stop filtering and return immediately. By this fix, there is only 3 values not throwing exception (Z_OK, Z_STREAM_END, Z_BUF_ERROR).  
If we decide to add another value (e.g. Z_MEM_ERROR) to this noexception list, the filter function shall return false for that error code too, otherwise the symmetric_filter will enter an infinite loop.  
So that returning (result == zlib::okay) is the right way.

> Username: yanikibo  
> Created_at: 2016-12-05 18:46:35 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90929151  

zlib::okay means that the operation is successfull and there is more data to be processed. This is exactly what symmetric_filter expects to continue filtering.


---

## Review 2 [Commented]

> Username: eldiener  
> Created_at: 2016-12-04 12:08:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/17#pullrequestreview-11293413  

📁 include/boost/iostreams/filter/zlib.hpp

```diff
 384 |-     return !(eof_ = result == zlib::stream_end);
 384 |+     eof_ = result == zlib::stream_end;
 385 |+     return result == zlib::okay;
```

> Username: eldiener  
> Created_at: 2016-12-04 12:08:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90776016  

I do not think your change here is correct as it follows the same logic as when xdeflate was called.

> Username: yanikibo  
> Created_at: 2016-12-04 17:09:15 UTC  
> Updated_at: 2016-12-04 17:53:33 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90781226  

The return value is used to flush the buffer, not to check the stream_end. Instead the eof_ variable is used to check the end of stream case.


---

## Review 3 [Commented]

> Username: eldiener  
> Created_at: 2016-12-04 12:12:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/17#pullrequestreview-11293476  

📁 src/zlib.cpp

```diff
  78 |     case Z_STREAM_END: 
  79 |-     //case Z_BUF_ERROR: 
  79 |+     case Z_BUF_ERROR:
```

> Username: eldiener  
> Created_at: 2016-12-04 12:12:20 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90776092  

Please explain why you think this change should be made. You are eseentially saying that a buffer error should not throw an exception.

> Username: yanikibo  
> Created_at: 2016-12-04 17:22:02 UTC  
> Updated_at: 2016-12-04 17:45:32 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#discussion_r90781519  

Z_BUF_ERROR is not a fatal error, it's a recoverable error. If we treat it as an error and throw an exception, we can not recover it. To recover this error, we should flush the data, empty the buffer and retry.  
  
## 5. deflate() or inflate() returns Z_BUF_ERROR:  
_Before making the call, make sure that avail_in and avail_out are not zero. When setting the parameter flush equal to Z_FINISH, also make sure that avail_out is big enough to allow processing all pending input. Note that a Z_BUF_ERROR is not fatal--another call to deflate() or inflate() can be made with more input or output space. A Z_BUF_ERROR may in fact be unavoidable depending on how the functions are used, since it is not possible to tell whether or not there is more output pending when strm.avail_out returns with zero._  
Reference: http://www.zlib.net/zlib_faq.html#faq05


---

## Comment 4

> Username: eldiener  
> Created_at: 2016-12-06 00:32:36 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#issuecomment-265024092  

The file_descriptor change broke the file descriptor tests so i am going to revert the file descriptor changes you made until I can look at them more closely and sync them with the file descriptor tests.

---

## Comment 5

> Username: eldiener  
> Created_at: 2016-12-06 19:29:17 UTC  
> Url: https://github.com/boostorg/iostreams/pull/17#issuecomment-265247749  

Some other change broke the gzip test, so I have backed out all the changes. I will be testing them out individually and re-instating them individually with any necessary fixes. I do thank you for this PR, even if it did not work out.

---
